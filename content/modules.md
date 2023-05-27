<!-- ### Modules -->
### Módulos / Modules

<!-- A module is a class annotated with a `@Module()` decorator. The `@Module()` decorator provides metadata that **Nest** makes use of to organize the application structure. -->

Un módulo es una clase anotada con un decorador `@Module()`. El decorador `@Module()` proporciona metadatos que **Nest** utiliza para organizar la estructura de la aplicación.

<figure><img src="/assets/Modules_1.png" /></figure>

<!-- Each application has at least one module, a **root module**. The root module is the starting point Nest uses to build the **application graph** - the internal data structure Nest uses to resolve module and provider relationships and dependencies. While very small applications may theoretically have just the root module, this is not the typical case. We want to emphasize that modules are **strongly** recommended as an effective way to organize your components. Thus, for most applications, the resulting architecture will employ multiple modules, each encapsulating a closely related set of **capabilities**. -->

Cada aplicación tiene al menos un módulo, un **módulo raíz / root module**. El módulo raíz es el punto de partida que Nest usa para construir el **gráfo de la aplicación / app graph**: la estructura de datos interna que Nest usa para resolver las relaciones y dependencias entre módulos y proveedores. Si bien las aplicaciones muy pequeñas teóricamente pueden tener solo el módulo raíz, este no es el caso típico. Queremos resaltar que los módulos se recomiendan **enfáticamente** como una forma efectiva de organizar sus componentes. Por lo tanto, para la mayoría de las aplicaciones, la arquitectura resultante empleará varios módulos, cada uno de los cuales encapsulará un conjunto estrechamente relacionado de **capacidades**.

<!-- The `@Module()` decorator takes a single object whose properties describe the module: -->

El decorador `@Module()` toma un único objeto cuyas propiedades describen el módulo:

<!-- |               |                                                                                                                                                                                                          |
| ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `providers`   | the providers that will be instantiated by the Nest injector and that may be shared at least across this module                                                                                          |
| `controllers` | the set of controllers defined in this module which have to be instantiated                                                                                                                              |
| `imports`     | the list of imported modules that export the providers which are required in this module                                                                                                                 |
| `exports`     | the subset of `providers` that are provided by this module and should be available in other modules which import this module. You can use either the provider itself or just its token (`provide` value) | -->



|               |                                                                                                                                                                                                          |
| ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `providers`   | los proveedores que serán instanciados por el inyector de Nest y que pueden compartirse al menos a través de este modulo                                                                                          |
| `controllers` | el conjunto de controladores definidos en este módulo que deben ser instanciados                                                                                                                             |
| `imports`     | la lista de módulos importados que exportan los proveedores que se requieren en este módulo                                                                                                                |
| `exports`     | el subconjunto de `proveedores` que proporciona este módulo y debería estar disponible en otros módulos que importan este módulo. Puede usar el proveedor en sí o solo su token (valor `provide`) |


<!-- The module **encapsulates** providers by default. This means that it's impossible to inject providers that are neither directly part of the current module nor exported from the imported modules. Thus, you may consider the exported providers from a module as the module's public interface, or API. -->

El módulo **encapsula** proveedores por defecto. Esto significa que es imposible inyectar proveedores que no sean directamente parte del módulo actual ni exportados de los módulos importados. Por lo tanto, puede considerar los proveedores exportados de un módulo como la interfaz pública o API del módulo.

<!-- #### Feature modules / No supe como traducir este término -->
#### Feature modules

<!-- The `CatsController` and `CatsService` belong to the same application domain. As they are closely related, it makes sense to move them into a feature module. A feature module simply organizes code relevant for a specific feature, keeping code organized and establishing clear boundaries. This helps us manage complexity and develop with [SOLID](https://en.wikipedia.org/wiki/SOLID) principles, especially as the size of the application and/or team grow. -->

`CatsController` y `CatsService` pertenecen al mismo dominio de aplicación. Como están estrechamente relacionados, tiene sentido moverlos a un 'feature module'. Un 'feature module' simplemente organiza el código relevante para una caraterística específica, manteniendo el código organizado y estableciendo límites claros. Esto nos ayuda a gestionar la complejidad y desarrollar con principios [SOLID](https://en.wikipedia.org/wiki/SOLID), especialmente a medida que crece el tamaño de la aplicación y/o el equipo.

<!-- To demonstrate this, we'll create the `CatsModule`. -->
Para demostrar esto, crearemos el `CatsModule`.

```typescript
@@filename(cats/cats.module)
import { Module } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Module({
  controllers: [CatsController],
  providers: [CatsService],
})
export class CatsModule {}
```

<!-- > info **Hint** To create a module using the CLI, simply execute the `$ nest g module cats` command. -->
> info **Sugerencia** Para crear un módulo usando la CLI, simplemente ejecute el comando `$ nest g module cats`.

<!-- Above, we defined the `CatsModule` in the `cats.module.ts` file, and moved everything related to this module into the `cats` directory. The last thing we need to do is import this module into the root module (the `AppModule`, defined in the `app.module.ts` file). -->

Arriba, definimos el `CatsModule` en el archivo `cats.module.ts` y movimos todo lo relacionado con este módulo al directorio `cats`. Lo último que debemos hacer es importar este módulo al módulo raíz (el `AppModule`, definido en el archivo `app.module.ts`).

```typescript
@@filename(app.module)
import { Module } from '@nestjs/common';
import { CatsModule } from './cats/cats.module';

@Module({
  imports: [CatsModule],
})
export class AppModule {}
```

<!-- Here is how our directory structure looks now: -->
Así es como se ve nuestra estructura en el directorio:

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
      <div class="item">cats.module.ts</div>
      <div class="item">cats.service.ts</div>
    </div>
    <div class="item">app.module.ts</div>
    <div class="item">main.ts</div>
  </div>
</div>

<!-- #### Shared modules -->
#### Módulos compartidos / Shared modules

<!-- In Nest, modules are **singletons** by default, and thus you can share the same instance of any provider between multiple modules effortlessly. -->

En Nest, los módulos son **singletons** de forma predeterminada y, por lo tanto, puede compartir la misma instancia de cualquier proveedor entre varios módulos sin esfuerzo.

<figure><img src="/assets/Shared_Module_1.png" /></figure>

<!-- Every module is automatically a **shared module**. Once created it can be reused by any module. Let's imagine that we want to share an instance of the `CatsService` between several other modules. In order to do that, we first need to **export** the `CatsService` provider by adding it to the module's `exports` array, as shown below: -->

Cada módulo es automáticamente un **módulo compartido / shared module**. Una vez creado, puede ser reutilizado por cualquier módulo. Imaginemos que queremos compartir una instancia de `CatsService` entre varios otros módulos. Para hacerlo, primero debemos **exportar** el proveedor `CatsService` agregándolo a la matriz `exportaciones` del módulo, como se muestra a continuación:

```typescript
@@filename(cats.module)
import { Module } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Module({
  controllers: [CatsController],
  providers: [CatsService],
  exports: [CatsService]
})
export class CatsModule {}
```

<!-- Now any module that imports the `CatsModule` has access to the `CatsService` and will share the same instance with all other modules that import it as well. -->

Ahora, cualquier módulo que importe `CatsModule` tiene acceso a `CatsService` y compartirá la misma instancia con todos los demás módulos que también lo importen.

<app-banner-devtools></app-banner-devtools>

<!-- #### Module re-exporting -->
#### Reexportación del módulo / Module re-exporting

<!-- As seen above, Modules can export their internal providers. In addition, they can re-export modules that they import. In the example below, the `CommonModule` is both imported into **and** exported from the `CoreModule`, making it available for other modules which import this one. -->

Como se vio anteriormente, los Módulos pueden exportar sus proveedores internos. Además, pueden volver a exportar módulos que importen. En el siguiente ejemplo, `CommonModule` se importa a **y** se exporta desde `CoreModule`, lo que lo hace disponible para otros módulos que importan este.

```typescript
@Module({
  imports: [CommonModule],
  exports: [CommonModule],
})
export class CoreModule {}
```

<!-- #### Dependency injection -->
#### Inyección de dependencias / Dependency injection

<!-- A module class can **inject** providers as well (e.g., for configuration purposes): -->
Una clase de módulo también puede **inyectar** proveedores (por ejemplo, para fines de configuración):

```typescript
@@filename(cats.module)
import { Module } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Module({
  controllers: [CatsController],
  providers: [CatsService],
})
export class CatsModule {
  constructor(private catsService: CatsService) {}
}
@@switch
import { Module, Dependencies } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Module({
  controllers: [CatsController],
  providers: [CatsService],
})
@Dependencies(CatsService)
export class CatsModule {
  constructor(catsService) {
    this.catsService = catsService;
  }
}
```

<!-- However, module classes themselves cannot be injected as providers due to [circular dependency](/fundamentals/circular-dependency) . -->

Sin embargo, las clases de módulos en sí mismas no se pueden inyectar como proveedores debido a la [circular dependency / dependecia circualr](/fundamentals/circular-dependency) .

<!-- #### Global modules -->
#### Módulos globales / Global modules

<!-- If you have to import the same set of modules everywhere, it can get tedious. Unlike in Nest, [Angular](https://angular.io) `providers` are registered in the global scope. Once defined, they're available everywhere. Nest, however, encapsulates providers inside the module scope. You aren't able to use a module's providers elsewhere without first importing the encapsulating module. -->

Si tiene que importar el mismo conjunto de módulos en todas partes, puede volverse tedioso. A diferencia de Nest, los `proveedores` de [Angular](https://angular.io) están registrados en el ámbito global. Una vez definidos, están disponibles en todas partes. Sin embargo, Nest encapsula a los proveedores dentro del alcance del módulo. No puede usar los proveedores de un módulo en otro lugar sin importar primero el módulo de encapsulación.

<!-- When you want to provide a set of providers which should be available everywhere out-of-the-box (e.g., helpers, database connections, etc.), make the module **global** with the `@Global()` decorator. -->

Cuando desee proporcionar un conjunto de proveedores que deberían estar disponibles en todas partes listos para usar (por ejemplo, ayudantes, conexiones de bases de datos, etc.), haga que el módulo sea **global** con el decorador `@Global()` .


```typescript
import { Module, Global } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Global()
@Module({
  controllers: [CatsController],
  providers: [CatsService],
  exports: [CatsService],
})
export class CatsModule {}
```

<!-- The `@Global()` decorator makes the module global-scoped. Global modules should be registered **only once**, generally by the root or core module. In the above example, the `CatsService` provider will be ubiquitous, and modules that wish to inject the service will not need to import the `CatsModule` in their imports array. -->

El decorador `@Global()` hace que el módulo tenga un alcance global. Los módulos globales deben registrarse **solo una vez**, generalmente por el módulo raíz o principal. En el ejemplo anterior, el proveedor `CatsService` será ubicuo, y los módulos que deseen inyectar el servicio no necesitarán importar `CatsModule` en su matriz de importaciones.

<!-- > info **Hint** Making everything global is not a good design decision. Global modules are available to reduce the amount of necessary boilerplate. The `imports` array is generally the preferred way to make the module's API available to consumers. -->

> info **Sugerencia** Hacer que todo sea global no es una buena decisión de diseño. Los módulos globales están disponibles para reducir la cantidad de repetitivo necesario. El array `imports` es generalmente la forma preferida de hacer que la API del módulo esté disponible para los consumidores.

<!-- #### Dynamic modules -->
#### Módulos dinámicos / Dynamic modules

<!-- The Nest module system includes a powerful feature called **dynamic modules**. This feature enables you to easily create customizable modules that can register and configure providers dynamically. Dynamic modules are covered extensively [here](/fundamentals/dynamic-modules). In this chapter, we'll give a brief overview to complete the introduction to modules. -->

El sistema de módulos Nest incluye un potente 'feature' llamado **módulos dinámicos / dynamic modules**. Esta característica le permite crear fácilmente módulos personalizables que pueden registrar y configurar proveedores dinámicamente. Los módulos dinámicos están ampliamente cubiertos [aquí](/fundamentals/dynamic-modules). En este capítulo, daremos una breve descripción general para completar la introducción a los módulos.

<!-- Following is an example of a dynamic module definition for a `DatabaseModule`: -->
El siguiente es un ejemplo de una definición de módulo dinámico para un `DatabaseModule`:

```typescript
@@filename()
import { Module, DynamicModule } from '@nestjs/common';
import { createDatabaseProviders } from './database.providers';
import { Connection } from './connection.provider';

@Module({
  providers: [Connection],
})
export class DatabaseModule {
  static forRoot(entities = [], options?): DynamicModule {
    const providers = createDatabaseProviders(options, entities);
    return {
      module: DatabaseModule,
      providers: providers,
      exports: providers,
    };
  }
}
@@switch
import { Module } from '@nestjs/common';
import { createDatabaseProviders } from './database.providers';
import { Connection } from './connection.provider';

@Module({
  providers: [Connection],
})
export class DatabaseModule {
  static forRoot(entities = [], options) {
    const providers = createDatabaseProviders(options, entities);
    return {
      module: DatabaseModule,
      providers: providers,
      exports: providers,
    };
  }
}
```

<!-- > info **Hint** The `forRoot()` method may return a dynamic module either synchronously or asynchronously (i.e., via a `Promise`). -->
> info **Sugerencia** El método `forRoot()` puede devolver un módulo dinámico de forma sincrónica o asincrónica (es decir, a través de una `Promesa`).

<!-- This module defines the `Connection` provider by default (in the `@Module()` decorator metadata), but additionally - depending on the `entities` and `options` objects passed into the `forRoot()` method - exposes a collection of providers, for example, repositories. Note that the properties returned by the dynamic module **extend** (rather than override) the base module metadata defined in the `@Module()` decorator. That's how both the statically declared `Connection` provider **and** the dynamically generated repository providers are exported from the module. -->

Este módulo define el proveedor `Connection` por defecto (en los metadatos del decorador `@Module()`), pero además, dependiendo de los objetos `entities` y `options` pasados al método `forRoot()`, expone un conjunto de proveedores, por ejemplo, repositorios. Tenga en cuenta que las propiedades devueltas por el módulo dinámico **extienden** (en lugar de anular) los metadatos del módulo base definidos en el decorador `@Module()`. Así es como tanto el proveedor de `Connection` declarado estáticamente **como** los proveedores de repositorio generados dinámicamente se exportan desde el módulo.

<!-- If you want to register a dynamic module in the global scope, set the `global` property to `true`. -->

Si desea registrar un módulo dinámico en el ámbito global, establezca la propiedad `global` en `true`.

```typescript
{
  global: true,
  module: DatabaseModule,
  providers: providers,
  exports: providers,
}
```

<!-- > warning **Warning** As mentioned above, making everything global **is not a good design decision**. -->
> warning **Advertencia** Como se mencionó anteriormente, hacer que todo sea global **no es una buena decisión de diseño**.

<!-- The `DatabaseModule` can be imported and configured in the following manner: -->
El `DatabaseModule` se puede importar y configurar de la siguiente manera:

```typescript
import { Module } from '@nestjs/common';
import { DatabaseModule } from './database/database.module';
import { User } from './users/entities/user.entity';

@Module({
  imports: [DatabaseModule.forRoot([User])],
})
export class AppModule {}
```

<!-- If you want to in turn re-export a dynamic module, you can omit the `forRoot()` method call in the exports array: -->
Si a su vez desea volver a exportar un módulo dinámico, puede omitir la llamada al método `forRoot()` en la matriz de exportaciones:

```typescript
import { Module } from '@nestjs/common';
import { DatabaseModule } from './database/database.module';
import { User } from './users/entities/user.entity';

@Module({
  imports: [DatabaseModule.forRoot([User])],
  exports: [DatabaseModule],
})
export class AppModule {}
```

<!-- The [Dynamic modules](/fundamentals/dynamic-modules) chapter covers this topic in greater detail, and includes a [working example](https://github.com/nestjs/nest/tree/master/sample/25-dynamic-modules). -->

El capítulo [Módulos dinámicos](/fundamentals/dynamic-modules) cubre este tema con mayor detalle e incluye un [ejemplo funcional](https://github.com/nestjs/nest/tree/master/sample/25-dynamic-modules).

<!-- > info **Hint** Learn how to build highly customizable dynamic modules with the use of `ConfigurableModuleBuilder` here in [this chapter](/fundamentals/dynamic-modules#configurable-module-builder). -->

> info **Sugerencia** Aprenda cómo construir módulos dinámicos altamente personalizables con el uso de `ConfigurableModuleBuilder` aquí en [este capítulo](/fundamentals/dynamic-modules#configurable-module-builder).
