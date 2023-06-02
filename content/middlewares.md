<!-- ### Middleware -->
### Middleware / Intermediarios

<!-- Middleware is a function which is called **before** the route handler. Middleware functions have access to the [request](https://expressjs.com/en/4x/api.html#req) and [response](https://expressjs.com/en/4x/api.html#res) objects, and the `next()` middleware function in the application’s request-response cycle. The **next** middleware function is commonly denoted by a variable named `next`. -->

El middleware es una función que se llama **antes** del controlador de ruta. Las funciones middleware tienen acceso a [solicitud/request](https://expressjs.com/en/4x/api.html#req), a la [respuesta/response](https://expressjs.com/en/4x/api.html#res ) y la función de middleware `next()` en el ciclo de solicitud-respuesta(request-response) de la aplicación. La función de middleware **next(siguiente)** se indica comúnmente mediante una variable llamada `next`.

<figure><img src="/assets/Middlewares_1.png" /></figure>

<!-- Nest middleware are, by default, equivalent to [express](https://expressjs.com/en/guide/using-middleware.html) middleware. The following description from the official express documentation describes the capabilities of middleware: -->

El middleware de Nest es, de forma predeterminada, equivalente al middleware [express](https://expressjs.com/en/guide/using-middleware.html). La siguiente descripción de la documentación express oficial describe las capacidades del middleware:

<!-- <blockquote class="external">
  Middleware functions can perform the following tasks:
  <ul>
    <li>execute any code.</li>
    <li>make changes to the request and the response objects.</li>
    <li>end the request-response cycle.</li>
    <li>call the next middleware function in the stack.</li>
    <li>if the current middleware function does not end the request-response cycle, it must call <code>next()</code> to
      pass control to the next middleware function. Otherwise, the request will be left hanging.</li>
  </ul>
</blockquote> -->


<blockquote class="external">
  Las funciones de middleware pueden realizar las siguientes tareas:
  <ul>
    <li>ejecutar cualquier código.</li>
    <li>realizar cambios en la solicitud y los objetos de respuesta.</li>
    <li>terminar el ciclo de solicitud-respuesta.</li>
    <li>llame a la siguiente función de middleware en la pila.</li>
    <li>si la función de middleware actual no finaliza el ciclo de solicitud-respuesta, debe llamar a <code>next()</code> para pasar el control a la siguiente función de middleware. De lo contrario, la solicitud quedará pendiente.</li>
  </ul>
</blockquote>

<!-- You implement custom Nest middleware in either a functikon, or in a class with an `@Injectable()` decorator. The class should implement the `NestMiddleware` interface, while the function does not have any special requirements. Let's start by implementing a simple middleware feature using the class method. -->

Implementa el middleware personalizado de Nest en una función o en una clase con un decorador `@Injectable()`. La clase debe implementar la interfaz `NestMiddleware`, mientras que la función no tiene requisitos especiales. Comencemos implementando una característica de middleware simple utilizando el método de clase.

<!-- >  warning **Warning** `Express` and `fastify` handle middleware differently and provide different method signatures, read more [here](/techniques/performance#middleware). -->

> warning **Advertencia** `Express` y `fastify` manejan el middleware de manera diferente y proporcionan diferentes firmas de métodos, lea más [aquí](/techniques/performance#middleware).


```typescript
@@filename(logger.middleware)
import { Injectable, NestMiddleware } from '@nestjs/common';
import { Request, Response, NextFunction } from 'express';

@Injectable()
export class LoggerMiddleware implements NestMiddleware {
  use(req: Request, res: Response, next: NextFunction) {
    console.log('Request...');
    next();
  }
}
@@switch
import { Injectable } from '@nestjs/common';

@Injectable()
export class LoggerMiddleware {
  use(req, res, next) {
    console.log('Request...');
    next();
  }
}
```

<!-- #### Dependency injection -->

#### Inyección de dependencias / Dependency injection


<!-- Nest middleware fully supports Dependency Injection. Just as with providers and controllers, they are able to **inject dependencies** that are available within the same module. As usual, this is done through the `constructor`. -->

El middleware de Nest es totalmente compatible con la inyección de dependencia. Al igual que con los proveedores y los controladores, pueden **inyectar dependencias** que están disponibles dentro del mismo módulo. Como de costumbre, esto se hace a través del `constructor`.

<!-- #### Applying middleware -->

#### Aplicando intermedriarios / Applying middleware

<!-- There is no place for middleware in the `@Module()` decorator. Instead, we set them up using the `configure()` method of the module class. Modules that include middleware have to implement the `NestModule` interface. Let's set up the `LoggerMiddleware` at the `AppModule` level. -->

No hay lugar para el middleware en el decorador `@Module()`. En cambio, lo configuramo usando el método `configure()` de la clase del módulo. Los módulos que incluyen middleware deben implementar la interfaz `NestModule`. Configuremos `LoggerMiddleware` en el nivel de `AppModule`.

```typescript
@@filename(app.module)
import { Module, NestModule, MiddlewareConsumer } from '@nestjs/common';
import { LoggerMiddleware } from './common/middleware/logger.middleware';
import { CatsModule } from './cats/cats.module';

@Module({
  imports: [CatsModule],
})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(LoggerMiddleware)
      .forRoutes('cats');
  }
}
@@switch
import { Module } from '@nestjs/common';
import { LoggerMiddleware } from './common/middleware/logger.middleware';
import { CatsModule } from './cats/cats.module';

@Module({
  imports: [CatsModule],
})
export class AppModule {
  configure(consumer) {
    consumer
      .apply(LoggerMiddleware)
      .forRoutes('cats');
  }
}
```

<!-- In the above example we have set up the `LoggerMiddleware` for the `/cats` route handlers that were previously defined inside the `CatsController`. We may also further restrict a middleware to a particular request method by passing an object containing the route `path` and request `method` to the `forRoutes()` method when configuring the middleware. In the example below, notice that we import the `RequestMethod` enum to reference the desired request method type. -->

En el ejemplo anterior, hemos configurado `LoggerMiddleware` para los controladores de ruta `/cats` que se definieron previamente dentro de `CatsController`. También podemos restringir aún más un middleware a un método de solicitud en particular pasando un objeto que contiene la ruta `path` y el `método` de la solicitud al método `forRoutes()` al configurar el middleware. En el siguiente ejemplo, observe que importamos la enumeración `RequestMethod` para hacer referencia al tipo de método de solicitud deseado.

```typescript
@@filename(app.module)
import { Module, NestModule, RequestMethod, MiddlewareConsumer } from '@nestjs/common';
import { LoggerMiddleware } from './common/middleware/logger.middleware';
import { CatsModule } from './cats/cats.module';

@Module({
  imports: [CatsModule],
})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(LoggerMiddleware)
      .forRoutes({ path: 'cats', method: RequestMethod.GET });
  }
}
@@switch
import { Module, RequestMethod } from '@nestjs/common';
import { LoggerMiddleware } from './common/middleware/logger.middleware';
import { CatsModule } from './cats/cats.module';

@Module({
  imports: [CatsModule],
})
export class AppModule {
  configure(consumer) {
    consumer
      .apply(LoggerMiddleware)
      .forRoutes({ path: 'cats', method: RequestMethod.GET });
  }
}
```

<!-- > info **Hint** The `configure()` method can be made asynchronous using `async/await` (e.g., you can `await` completion of an asynchronous operation inside the `configure()` method body). -->

> info **Sugerencia** El método `configure()` se puede hacer asíncrono usando `async/await` (por ejemplo, puede colocar `await` en la finalización de una operación asíncrona dentro del cuerpo del método `configure()`).

<!-- > warning **Warning** When using the `express` adapter, the NestJS app will register `json` and `urlencoded` from the package `body-parser` by default. This means if you want to customize that middleware via the `MiddlewareConsumer`, you need to turn off the global middleware by setting the `bodyParser` flag to `false` when creating the application with `NestFactory.create()`. -->

> warning **Advertencia** Al usar el adaptador `express`, la aplicación NestJS registrará `json` y `urlencoded` del paquete `body-parser` de manera predeterminada. Esto significa que si desea personalizar ese middleware a través de `MiddlewareConsumer`, debe desactivar el middleware global configurando el indicador `bodyParser` en `false` al crear la aplicación con `NestFactory.create()`.

<!-- #### Route wildcards -->

#### Comodines de ruta / Route wildcards

<!-- Pattern based routes are supported as well. For instance, the asterisk is used as a **wildcard**, and will match any combination of characters: -->

Las rutas basadas en patrones también son compatibles. Por ejemplo, el asterisco se usa como **wildcard / comodín** y coincidirá con cualquier combinación de caracteres:

```typescript
forRoutes({ path: 'ab*cd', method: RequestMethod.ALL });
```

<!-- The `'ab*cd'` route path will match `abcd`, `ab_cd`, `abecd`, and so on. The characters `?`, `+`, `*`, and `()` may be used in a route path, and are subsets of their regular expression counterparts. The hyphen ( `-`) and the dot (`.`) are interpreted literally by string-based paths. -->

La ruta de la ruta `'ab*cd'` coincidirá con `abcd`, `ab_cd`, `abecd`, etc. Los caracteres `?`, `+`, `*` y `()` se pueden usar en una ruta de ruta y son subconjuntos de sus equivalentes de expresiones regulares. El guión (`-`) y el punto (`.`) se interpretan literalmente mediante rutas basadas en cadenas.

<!-- > warning **Warning** The `fastify` package uses the latest version of the `path-to-regexp` package, which no longer supports wildcard asterisks `*`. Instead, you must use parameters (e.g., `(.*)`, `:splat*`). -->

> warning **Advertencia** El paquete `fastify` utiliza la última versión del paquete `path-to-regexp`, que ya no admite asteriscos comodín `*`. En su lugar, debe usar parámetros (por ejemplo, `(.*)`, `:splat*`).

<!-- #### Middleware consumer -->

#### Consumidor de intermediarios / Middleware consumer

<!-- The `MiddlewareConsumer` is a helper class. It provides several built-in methods to manage middleware. All of them can be simply **chained** in the [fluent style](https://en.wikipedia.org/wiki/Fluent_interface). The `forRoutes()` method can take a single string, multiple strings, a `RouteInfo` object, a controller class and even multiple controller classes. In most cases you'll probably just pass a list of **controllers** separated by commas. Below is an example with a single controller: -->

`MiddlewareConsumer` es una clase auxiliar. Proporciona varios métodos integrados para gestionar el middleware. Todos ellos pueden ser simplemente **encadenados(chained)** en el [estilo fluido(fluent style)](https://en.wikipedia.org/wiki/Fluent_interface). El método `forRoutes()` puede tomar una sola cadena, varias cadenas, un objeto `RouteInfo`, una clase de controlador e incluso varias clases de controlador. En la mayoría de los casos, probablemente pasará una lista de ***controllers** separados por comas. A continuación se muestra un ejemplo con un solo controlador:

```typescript
@@filename(app.module)
import { Module, NestModule, MiddlewareConsumer } from '@nestjs/common';
import { LoggerMiddleware } from './common/middleware/logger.middleware';
import { CatsModule } from './cats/cats.module';
import { CatsController } from './cats/cats.controller';

@Module({
  imports: [CatsModule],
})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(LoggerMiddleware)
      .forRoutes(CatsController);
  }
}
@@switch
import { Module } from '@nestjs/common';
import { LoggerMiddleware } from './common/middleware/logger.middleware';
import { CatsModule } from './cats/cats.module';
import { CatsController } from './cats/cats.controller';

@Module({
  imports: [CatsModule],
})
export class AppModule {
  configure(consumer) {
    consumer
      .apply(LoggerMiddleware)
      .forRoutes(CatsController);
  }
}
```

<!-- > info **Hint** The `apply()` method may either take a single middleware, or multiple arguments to specify <a href="/middleware#multiple-middleware">multiple middlewares</a>. -->

> info **Sugerencia** El método `apply()` puede tomar un solo middleware o múltiples argumentos para especificar <a href="/middleware#multiple-middleware">múltiples middlewares</a>.

<!-- #### Excluding routes -->

#### Excluyendo rutas / Excluding routes

<!-- At times we want to **exclude** certain routes from having the middleware applied. We can easily exclude certain routes with the `exclude()` method. This method can take a single string, multiple strings, or a `RouteInfo` object identifying routes to be excluded, as shown below: -->

A veces queremos **excluir** ciertas rutas para que no se aplique el middleware. Podemos excluir fácilmente ciertas rutas con el método `exclude()`. Este método puede tomar una sola cadena, varias cadenas o un objeto `RouteInfo` que identifique las rutas que se van a excluir, como se muestra a continuación:

```typescript
consumer
  .apply(LoggerMiddleware)
  .exclude(
    { path: 'cats', method: RequestMethod.GET },
    { path: 'cats', method: RequestMethod.POST },
    'cats/(.*)',
  )
  .forRoutes(CatsController);
```

<!-- > info **Hint** The `exclude()` method supports wildcard parameters using the [path-to-regexp](https://github.com/pillarjs/path-to-regexp#parameters) package. -->

> info **Sugerencia** El método `exclude()` admite parámetros comodín usando el paquete [path-to-regexp](https://github.com/pillarjs/path-to-regexp#parameters).

<!-- With the example above, `LoggerMiddleware` will be bound to all routes defined inside `CatsController` **except** the three passed to the `exclude()` method. -->

Con el ejemplo anterior, `LoggerMiddleware` se vinculará a todas las rutas definidas dentro de `CatsController` **excepto** las tres pasadas al método `exclude()`.

<!-- #### Functional middleware -->

#### Intermediario funcional / Functional middleware

The `LoggerMiddleware` class we've been using is quite simple. It has no members, no additional methods, and no dependencies. Why can't we just define it in a simple function instead of a class? In fact, we can. This type of middleware is called **functional middleware**. Let's transform the logger middleware from class-based into functional middleware to illustrate the difference:

La clase `LoggerMiddleware` que hemos estado usando es bastante simple. No tiene miembros, ni métodos adicionales, ni dependencias. ¿Por qué no podemos simplemente definirlo en una función simple en lugar de una clase? De hecho, podemos. Este tipo de middleware se denomina **middleware funcional(functional middleware)**. Transformemos el middleware del registrador de un middleware basado en clases en un middleware funcional para ilustrar la diferencia:

```typescript
@@filename(logger.middleware)
import { Request, Response, NextFunction } from 'express';

export function logger(req: Request, res: Response, next: NextFunction) {
  console.log(`Request...`);
  next();
};
@@switch
export function logger(req, res, next) {
  console.log(`Request...`);
  next();
};
```

<!-- And use it within the `AppModule`: -->
Y utilícelo dentro del `AppModule`:

```typescript
@@filename(app.module)
consumer
  .apply(logger)
  .forRoutes(CatsController);
```

<!-- > info **Hint** Consider using the simpler **functional middleware** alternative any time your middleware doesn't need any dependencies. -->

> info **Sugerencia** Considere usar la alternativa de **intermediario funcional** más simple cada vez que su middleware no necesite ninguna dependencia.

<!-- #### Multiple middleware -->

#### Múltiples intermediarios / Multiple middleware

<!-- As mentioned above, in order to bind multiple middleware that are executed sequentially, simply provide a comma separated list inside the `apply()` method: -->

Como se mencionó anteriormente, para vincular varios middleware que se ejecutan secuencialmente, simplemente proporcione una lista separada por comas dentro del método `apply()`:

```typescript
consumer.apply(cors(), helmet(), logger).forRoutes(CatsController);
```

<!-- #### Global middleware -->

#### Intermediario global / Global middleware

<!-- If we want to bind middleware to every registered route at once, we can use the `use()` method that is supplied by the `INestApplication` instance: -->

Si queremos vincular el middleware a todas las rutas registradas a la vez, podemos usar el método `use()` que proporciona la instancia `INestApplication`:

```typescript
@@filename(main)
const app = await NestFactory.create(AppModule);
app.use(logger);
await app.listen(3000);
```

<!-- > info **Hint** Accessing the DI container in a global middleware is not possible. You can use a [functional middleware](middleware#functional-middleware) instead when using `app.use()`. Alternatively, you can use a class middleware and consume it with `.forRoutes('*')` within the `AppModule` (or any other module). -->

> info **Sugerencia** No es posible acceder al contenedor DI en un intermediario/middleware global. Puedes usar un [intermediario/middleware funcional](middleware#funcional-middleware) en su lugar cuando usas `app.use()`. Alternativamente, puede usar un middleware de clase y consumirlo con `.forRoutes('*')` dentro del `AppModule` (o cualquier otro módulo).
