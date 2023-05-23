<!-- ### Providers -->
### Providers/Provedores

<!-- Providers are a fundamental concept in Nest. Many of the basic Nest classes may be treated as a provider – services, repositories, factories, helpers, and so on. The main idea of a provider is that it can be **injected** as a dependency; this means objects can create various relationships with each other, and the function of "wiring up" instances of objects can largely be delegated to the Nest runtime system. -->

Los proveedores son un concepto fundamental en Nest. Muchas de las clases básicas de Nest pueden tratarse como un proveedor: servicios, repositorios, fábricas, ayudantes, etc. La idea principal de un proveedor es que puede ser **inyectado** como una dependencia; esto significa que los objetos pueden crear diversas relaciones entre sí, y la función de "conectar (wiring up)" instancias de objetos puede delegarse en gran medida al runtime de Nest.

<figure><img src="/assets/Components_1.png" /></figure>

<!-- In the previous chapter, we built a simple `CatsController`. Controllers should handle HTTP requests and delegate more complex tasks to **providers**. Providers are plain JavaScript classes that are declared as `providers` in a [module](/modules). -->

En el capítulo anterior, construimos un `CatsController` simple. Los controladores deben manejar las solicitudes HTTP y delegar tareas más complejas a los **providers/proveedores**. Los proveedores son clases simples de JavaScript que se declaran como `providers` en un [módulo](/módulos)

<!-- > info **Hint** Since Nest enables the possibility to design and organize dependencies in a more OO way, we strongly recommend following the [SOLID](https://en.wikipedia.org/wiki/SOLID) principles. -->

> info **Pista** Dado que Nest permite la posibilidad de diseñar y organizar dependencias de una manera más propia de la OOP/POO, recomendamos encarecidamente seguir los principios [SOLID](https://en.wikipedia.org/wiki/SOLID).

<!-- #### Services -->
#### Services/Servicios

<!-- Let's start by creating a simple `CatsService`. This service will be responsible for data storage and retrieval, and is designed to be used by the `CatsController`, so it's a good candidate to be defined as a provider. -->

Comencemos por crear un `CatsService` simple. Este servicio será responsable del almacenamiento y recuperación de datos, y está diseñado para ser utilizado por `CatsController`, por lo que es un buen candidato para ser definido como proveedor.

```typescript
@@filename(cats.service)
import { Injectable } from '@nestjs/common';
import { Cat } from './interfaces/cat.interface';

@Injectable()
export class CatsService {
  private readonly cats: Cat[] = [];

  create(cat: Cat) {
    this.cats.push(cat);
  }

  findAll(): Cat[] {
    return this.cats;
  }
}
@@switch
import { Injectable } from '@nestjs/common';

@Injectable()
export class CatsService {
  constructor() {
    this.cats = [];
  }

  create(cat) {
    this.cats.push(cat);
  }

  findAll() {
    return this.cats;
  }
}
```

<!-- > info **Hint** To create a service using the CLI, simply execute the `$ nest g service cats` command. -->
> info **Sugerencia** Para crear un servicio usando la CLI, simplemente ejecute el comando `$ nest g service cats`.

<!-- Our `CatsService` is a basic class with one property and two methods. The only new feature is that it uses the `@Injectable()` decorator. The `@Injectable()` decorator attaches metadata, which declares that `CatsService`  is a class that can be managed by the Nest [IoC](https://en.wikipedia.org/wiki/Inversion_of_control) container. By the way, this example also uses a `Cat` interface, which probably looks something like this: -->

Nuestro `CatsService` es una clase básica con una propiedad y dos métodos. La única característica nueva es que utiliza el decorador `@Injectable()`. El decorador `@Injectable()` adjunta metadatos, que declara que `CatsService` es una clase que puede ser administrada por el contenedor Nest [IoC](https://en.wikipedia.org/wiki/Inversion_of_control). Por cierto, este ejemplo también usa una interfaz `Cat`, que probablemente se parece a esto:

```typescript
@@filename(interfaces/cat.interface)
export interface Cat {
  name: string;
  age: number;
  breed: string;
}
```

<!-- Now that we have a service class to retrieve cats, let's use it inside the `CatsController`: -->
Ahora que tenemos una clase de servicio para recuperar 'cats', usémosla dentro de `CatsController`:

```typescript
@@filename(cats.controller)
import { Controller, Get, Post, Body } from '@nestjs/common';
import { CreateCatDto } from './dto/create-cat.dto';
import { CatsService } from './cats.service';
import { Cat } from './interfaces/cat.interface';

@Controller('cats')
export class CatsController {
  constructor(private catsService: CatsService) {}

  @Post()
  async create(@Body() createCatDto: CreateCatDto) {
    this.catsService.create(createCatDto);
  }

  @Get()
  async findAll(): Promise<Cat[]> {
    return this.catsService.findAll();
  }
}
@@switch
import { Controller, Get, Post, Body, Bind, Dependencies } from '@nestjs/common';
import { CatsService } from './cats.service';

@Controller('cats')
@Dependencies(CatsService)
export class CatsController {
  constructor(catsService) {
    this.catsService = catsService;
  }

  @Post()
  @Bind(Body())
  async create(createCatDto) {
    this.catsService.create(createCatDto);
  }

  @Get()
  async findAll() {
    return this.catsService.findAll();
  }
}
```

<!-- The `CatsService` is **injected** through the class constructor. Notice the use of the `private` syntax. This shorthand allows us to both declare and initialize the `catsService` member immediately in the same location. -->

El `CatsService` se **inyecta** a través del constructor de la clase. Observe el uso de la sintaxis `private`. Esta abreviatura nos permite tanto declarar como inicializar el miembro `catsService` inmediatamente en la misma ubicación.

<!-- #### Dependency injection -->
#### Inyección de dependencias / Dependency injection

<!-- Nest is built around the strong design pattern commonly known as **Dependency injection**. We recommend reading a great article about this concept in the official [Angular](https://angular.io/guide/dependency-injection) documentation. -->

Nest se basa en un sólido patrón de diseño comúnmente conocido como **Inyección de dependencias**. Recomendamos leer un excelente artículo sobre este concepto en la documentación oficial de [Angular](https://angular.io/guide/dependency-injection).

<!-- In Nest, thanks to TypeScript capabilities, it's extremely easy to manage dependencies because they are resolved just by type. In the example below, Nest will resolve the `catsService` by creating and returning an instance of `CatsService` (or, in the normal case of a singleton, returning the existing instance if it has already been requested elsewhere). This dependency is resolved and passed to your controller's constructor (or assigned to the indicated property): -->

En Nest, gracias a las capacidades de TypeScript, es extremadamente fácil administrar las dependencias dado que se resuelven solo por tipo. En el siguiente ejemplo, Nest resolverá el `catsService` creando y devolviendo una instancia de `CatsService` (o, en el caso normal de un 'singleton', devolviendo la instancia existente si ya se solicitó en otro lugar). Esta dependencia se resuelve y pasa al constructor de su controlador (o se asigna a la propiedad indicada):

```typescript
constructor(private catsService: CatsService) {}
```

<!-- #### Scopes -->
#### Scopes / Alcance - Ámbito

Providers normally have a lifetime ("scope") synchronized with the application lifecycle. When the application is bootstrapped, every dependency must be resolved, and therefore every provider has to be instantiated. Similarly, when the application shuts down, each provider will be destroyed. However, there are ways to make your provider lifetime **request-scoped** as well. You can read more about these techniques [here](/fundamentals/injection-scopes).

Los proveedores normalmente tienen un ciclo de vida ("scope") sincronizado con el ciclo de vida de la aplicación. Cuando se inicia la aplicación, se deben resolver todas las dependencias y, por lo tanto, se debe crear una instancia de cada proveedor. Del mismo modo, cuando la aplicación se cierre, se destruirán todos los proveedores. Sin embargo, también hay maneras de hacer que su proveedor dure toda la vida **request-scoped**. Puede leer más sobre estas técnicas [aquí](/fundamentals/injection-scopes).

<app-banner-courses></app-banner-courses>

<!-- #### Custom providers -->
#### Proveedores personalizados / Custom providers

Nest has a built-in inversion of control ("IoC") container that resolves relationships between providers. This feature underlies the dependency injection feature described above, but is in fact far more powerful than what we've described so far. There are several ways to define a provider: you can use plain values, classes, and either asynchronous or synchronous factories. More examples are provided [here](/fundamentals/dependency-injection).

Nest tiene un contenedor de inversión de control ("IoC") integrado que resuelve las relaciones entre proveedores. Esta función es la base de la función de inyección de dependencia descrita anteriormente, pero de hecho es mucho más potente que lo que hemos descrito hasta ahora. Hay varias formas de definir un proveedor: puede usar valores simples, clases y fábricas asíncronas o síncronas. Se proporcionan más ejemplos [aquí](/fundamentals/dependency-injection).

#### Optional providers
#### Proveedores opcionales /Optional providers

<!-- Occasionally, you might have dependencies which do not necessarily have to be resolved. For instance, your class may depend on a **configuration object**, but if none is passed, the default values should be used. In such a case, the dependency becomes optional, because lack of the configuration provider wouldn't lead to errors. -->
Ocasionalmente, es posible que tenga dependencias que no necesariamente deben resolverse. Por ejemplo, su clase puede depender de un **configuration object / objeto de configuración**, pero si no se pasa ninguno, se deben usar los valores predeterminados. En tal caso, la dependencia se vuelve opcional, porque la falta del proveedor de configuración no generaría errores.

<!-- To indicate a provider is optional, use the `@Optional()` decorator in the constructor's signature. -->
Para indicar que un proveedor es opcional, use el decorador `@Optional()` en la firma del constructor.

```typescript
import { Injectable, Optional, Inject } from '@nestjs/common';

@Injectable()
export class HttpService<T> {
  constructor(@Optional() @Inject('HTTP_OPTIONS') private httpClient: T) {}
}
```

<!-- Note that in the example above we are using a custom provider, which is the reason we include the `HTTP_OPTIONS` custom **token**. Previous examples showed constructor-based injection indicating a dependency through a class in the constructor. Read more about custom providers and their associated tokens [here](/fundamentals/custom-providers). -->
Tenga en cuenta que en el ejemplo anterior estamos usando un proveedor personalizado, razón por la cual incluimos el **token** personalizado `HTTP_OPTIONS`. Los ejemplos anteriores mostraban una inyección basada en constructores que indicaba una dependencia a través de una clase en el constructor. Obtenga más información sobre los proveedores personalizados y sus tokens asociados [aquí](/fundamentals/custom-providers).

<!-- #### Property-based injection -->
#### Inyección basada en propiedades / Property-based injection

<!-- The technique we've used so far is called constructor-based injection, as providers are injected via the constructor method. In some very specific cases, **property-based injection** might be useful. For instance, if your top-level class depends on either one or multiple providers, passing them all the way up by calling `super()` in sub-classes from the constructor can be very tedious. In order to avoid this, you can use the `@Inject()` decorator at the property level. -->

La técnica que hemos usado hasta ahora se llama 'inyección basada en constructor', ya que los proveedores se inyectan a través del método constructor. En algunos casos muy específicos, la **inyección basada en propiedades** podría ser útil. Por ejemplo, si su clase de nivel superior depende de uno o varios proveedores, pasarlos completamente llamando a `super()` en las subclases del constructor puede ser muy tedioso. Para evitar esto, puede usar el decorador `@Inject()` en el nivel de propiedad.

```typescript
import { Injectable, Inject } from '@nestjs/common';

@Injectable()
export class HttpService<T> {
  @Inject('HTTP_OPTIONS')
  private readonly httpClient: T;
}
```

<!-- > warning **Warning** If your class doesn't extend another provider, you should always prefer using **constructor-based** injection. -->
> warning **Advertencia** Si su clase no se extiende a otro proveedor, siempre debe preferir usar la inyección **basada en constructores**.

<!-- #### Provider registration -->
#### Registro de proveedores / Provider registration

<!-- Now that we have defined a provider (`CatsService`), and we have a consumer of that service (`CatsController`), we need to register the service with Nest so that it can perform the injection. We do this by editing our module file (`app.module.ts`) and adding the service to the `providers` array of the `@Module()` decorator. -->

Ahora que hemos definido un proveedor (`CatsService`) y tenemos como consumir ese servicio (`CatsController`), necesitamos registrar el servicio con Nest para que pueda realizar la inyección. Hacemos esto editando nuestro archivo de módulo (`app.module.ts`) y agregando el servicio al array de `providers` del decorador `@Module()`.

```typescript
@@filename(app.module)
import { Module } from '@nestjs/common';
import { CatsController } from './cats/cats.controller';
import { CatsService } from './cats/cats.service';

@Module({
  controllers: [CatsController],
  providers: [CatsService],
})
export class AppModule {}
```

<!-- Nest will now be able to resolve the dependencies of the `CatsController` class. -->
Nest ahora podrá resolver las dependencias de la clase `CatsController`.

<!-- This is how our directory structure should look now: -->
Así es como debería verse ahora nuestra estructura de directorios:

<div class="file-tree">
<div class="item">src</div>
<div class="children">
<div class="item">cats</div>
<div class="children">
<div class="item">dto</div>
<div class="children">
<div class="item">create-cat.dto.ts</div>
</div>
<div class="item">interfaces</div>
<div class="children">
<div class="item">cat.interface.ts</div>
</div>
<div class="item">cats.controller.ts</div>
<div class="item">cats.service.ts</div>
</div>
<div class="item">app.module.ts</div>
<div class="item">main.ts</div>
</div>
</div>

<!-- #### Manual instantiation -->
#### Instanciación manual / Manual instantiation

<!-- Thus far, we've discussed how Nest automatically handles most of the details of resolving dependencies. In certain circumstances, you may need to step outside of the built-in Dependency Injection system and manually retrieve or instantiate providers. We briefly discuss two such topics below. -->
Hasta ahora, hemos discutido cómo Nest maneja automáticamente la mayoría de los detalles de la resolución de dependencias. En determinadas circunstancias, es posible que deba salir del sistema de Inyección de Dependencias y recuperar o crear instancias de proveedores de forma manual. Discutimos brevemente dos de estos temas a continuación.

<!-- To get existing instances, or instantiate providers dynamically, you can use [Module reference](https://docs.nestjs.com/fundamentals/module-ref). -->

Para obtener instancias existentes o crear instancias de proveedores dinámicamente, puede usar [Referencia del Módulo](https://docs.nestjs.com/fundamentals/module-ref).

<!-- To get providers within the `bootstrap()` function (for example for standalone applications without controllers, or to utilize a configuration service during bootstrapping) see [Standalone applications](https://docs.nestjs.com/standalone-applications). -->

Para obtener proveedores dentro de la función `bootstrap()` (por ejemplo, para aplicaciones independientes sin controladores, o para utilizar un servicio de configuración durante el arranque), consulte [Aplicaciones Independientes](https://docs.nestjs.com/standalone-applications).