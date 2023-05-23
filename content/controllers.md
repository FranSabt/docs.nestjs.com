<!-- ### Controllers -->
### Controladores

<!-- Controllers are responsible for handling incoming **requests** and returning **responses** to the client. -->
Los controladores son responsables de manejar las **solicitudes** entrantes y devolver **respuestas** al cliente.

<figure><img src="/assets/Controllers_1.png" /></figure>

<!-- A controller's purpose is to receive specific requests for the application. The **routing** mechanism controls which controller receives which requests. Frequently, each controller has more than one route, and different routes can perform different actions. -->
El propósito de un controlador es recibir solicitudes específicas para la aplicación. El mecanismo **routing** controla qué controlador recibe qué solicitudes. Con frecuencia, cada controlador tiene más de una ruta y diferentes rutas pueden realizar diferentes acciones.


<!-- In order to create a basic controller, we use classes and **decorators**. Decorators associate classes with required metadata and enable Nest to create a routing map (tie requests to the corresponding controllers). -->
Para crear un controlador básico, usamos clases y **decoradores (decorators)**. Los decoradores asocian clases con los metadatos necesarios y permiten a Nest crear un mapa de enrutamiento (vincular las solicitudes a los controladores correspondientes).


<!-- > info **Hint** For quickly creating a CRUD controller with the [validation](https://docs.nestjs.com/techniques/validation) built-in, you may use the CLI's [CRUD generator](https://docs.nestjs.com/recipes/crud-generator#crud-generator): `nest g resource [name]`. -->

> info **Sugerencia** Para crear rápidamente un controlador CRUD con la [validación](https://docs.nestjs.com/techniques/validation) incorporada, puede usar el  [CRUD generator](https://docs.nestjs.com/recipes/crud-generator#crud-generator): `nest g resource [name]`.

<!-- #### Routing -->
#### Ruteo/Routing

<!-- In the following example we'll use the `@Controller()` decorator, which is **required** to define a basic controller. We'll specify an optional route path prefix of `cats`. Using a path prefix in a `@Controller()` decorator allows us to easily group a set of related routes, and minimize repetitive code. For example, we may choose to group a set of routes that manage interactions with a cat entity under the route `/cats`. In that case, we could specify the path prefix `cats` in the `@Controller()` decorator so that we don't have to repeat that portion of the path for each route in the file. -->

En el siguiente ejemplo usaremos el decorador `@Controller()`, que es **requerido** para definir un controlador básico. Especificaremos un prefijo de ruta de ruta opcional de `cats`. El uso de un prefijo de ruta en un decorador `@Controller()` nos permite agrupar fácilmente un conjunto de rutas relacionadas y minimizar el código repetitivo. Por ejemplo, podemos optar por agrupar un conjunto de rutas que gestionan las interacciones con una entidad de gato bajo la ruta `/cats`. En ese caso, podríamos especificar el prefijo de ruta `cats` en el decorador `@Controller()` para que no tengamos que repetir esa parte de la ruta para cada ruta en el archivo.


```typescript
@@filename(cats.controller)
import { Controller, Get } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Get()
  findAll(): string {
    return 'This action returns all cats';
  }
}
@@switch
import { Controller, Get } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Get()
  findAll() {
    return 'This action returns all cats';
  }
}
```

<!-- > info **Hint** To create a controller using the CLI, simply execute the `$ nest g controller cats` command. -->

> info **Sugerencia** Para crear un controlador usando la CLI, simplemente ejecute el comando `$ nest g controller cats`.

<!-- The `@Get()` HTTP request method decorator before the `findAll()` method tells Nest to create a handler for a specific endpoint for HTTP requests. The endpoint corresponds to the HTTP request method (GET in this case) and the route path. What is the route path? The route path for a handler is determined by concatenating the (optional) prefix declared for the controller, and any path specified in the method's decorator. Since we've declared a prefix for every route ( `cats`), and haven't added any path information in the decorator, Nest will map `GET /cats` requests to this handler. As mentioned, the path includes both the optional controller path prefix **and** any path string declared in the request method decorator. For example, a path prefix of `cats` combined with the decorator `@Get('breed')` would produce a route mapping for requests like `GET /cats/breed`. -->

El decorador del método de solicitud HTTP  `@Get()` antes del método `findAll()` indica a Nest que cree un controlador para un punto de enlace específico para las solicitudes HTTP. El punto final corresponde al método de solicitud HTTP (GET en este caso) y el camino de  la ruta (route path). ¿Cuál es camino de  la ruta? Para un controlador se determina concatenando el prefijo (opcional) declarado para el controlador y cualquier ruta especificada en el decorador del método. Como hemos declarado un prefijo para cada ruta ( `cats`) y no hemos agregado ninguna información de ruta en el decorador, Nest asignará las solicitudes `GET /cats` a este controlador. Como se mencionó, la ruta incluye tanto el prefijo opcional de ruta del controlador **como** cualquier cadena de ruta declarada en el decorador del método de solicitud. Por ejemplo, un prefijo de ruta de `cats` combinado con el decorador `@Get('breed')` produciría un mapeo de ruta para solicitudes como `GET /cats/breed`.

<!-- In our example above, when a GET request is made to this endpoint, Nest routes the request to our user-defined `findAll()` method. Note that the method name we choose here is completely arbitrary. We obviously must declare a method to bind the route to, but Nest doesn't attach any significance to the method name chosen. -->
En nuestro ejemplo anterior, cuando se realiza una solicitud GET a este punto final, Nest enruta la solicitud a nuestro método `findAll()` definido por el usuario. Tenga en cuenta que el nombre del método que elegimos aquí es completamente arbitrario. Obviamente debemos declarar un método para enlazar la ruta, pero Nest no le da ningún significado al nombre del método elegido.

<!-- This method will return a 200 status code and the associated response, which in this case is just a string. Why does that happen? To explain, we'll first introduce the concept that Nest employs two **different** options for manipulating responses: -->
Este método devolverá un status code 200 y la respuesta asociada, que en este caso es solo una cadena. ¿Por qué sucede eso? Para explicarlo, primero presentaremos el concepto de que Nest emplea dos opciones **diferentes** para manipular las respuestas:

<!-- <table>
  <tr>
    <td>Standard (recommended)</td>
    <td>
      Using this built-in method, when a request handler returns a JavaScript object or array, it will <strong>automatically</strong>
      be serialized to JSON. When it returns a JavaScript primitive type (e.g., <code>string</code>, <code>number</code>, <code>boolean</code>), however, Nest will send just the value without attempting to serialize it. This makes response handling simple: just return the value, and Nest takes care of the rest.
      <br />
      <br /> Furthermore, the response's <strong>status code</strong> is always 200 by default, except for POST
      requests which use 201. We can easily change this behavior by adding the <code>@HttpCode(...)</code>
      decorator at a handler-level (see <a href='controllers#status-code'>Status codes</a>).
    </td>
  </tr>
  <tr>
    <td>Library-specific</td>
    <td>
      We can use the library-specific (e.g., Express) <a href="https://expressjs.com/en/api.html#res" rel="nofollow" target="_blank">response object</a>, which can be injected using the <code>@Res()</code> decorator in the method handler signature (e.g., <code>findAll(@Res() response)</code>).  With this approach, you have the ability to use the native response handling methods exposed by that object.  For example, with Express, you can construct responses using code like <code>response.status(200).send()</code>.
    </td>
  </tr>
</table> -->
<table>
  <tr>
    <td>Estandard (recomendada)</td>
    <td>
      Con este método integrado, cuando un controlador de solicitudes devuelve un objeto o matriz JavaScript,<strong>automáticamente</strong>
      ser serializado a JSON. Cuando devuelve un tipo primitivo de JavaScript (por ejemplo, <code>una cadena</code>, <code>numero</code>, <code>booleano</code>), sin embargo, Nest enviará solo el valor sin intentar serializarlo. Esto simplifica la gestión de respuestas: simplemente devuelve el valor y Nest se encarga del resto.
      <br />
      <br /> Además, la respuesta es .<strong>status code</strong> 200 siempre por default, excepto por perticiones
      POST que usan 201. Podemos cambiar fácilmente este comportamiento agregando el decorador <code>@HttpCode(...)</code>
       a nivel de controlador (para saber mas <a href='controllers#status-code'>Status codes</a>).
    </td>
  </tr>
  <tr>
    <td>Específico de la biblioteca</td>
    <td>
      Podemos usar una biblioteca específica (por ejemplo, Express) <a href="https://expressjs.com/en/api.html#res" rel="nofollow" target="_blank">response object</a>, el cuál puede ser inyectado con el decorador <code>@Res()</code> en el controlador de métodos (por ejemplo, <code>findAll(@Res() response)</code>). Con este enfoque, se tiene la capacidad de utilizar los métodos de manejo de respuestas nativos expuestos por ese objeto. Por ejemplo, con Express, puede construir respuestas usando código como <code>response.status(200).send()</code>.
    </td>
  </tr>
</table>

<!-- > warning **Warning** Nest detects when the handler is using either `@Res()` or `@Next()`, indicating you have chosen the library-specific option. If both approaches are used at the same time, the Standard approach is **automatically disabled** for this single route and will no longer work as expected. To use both approaches at the same time (for example, by injecting the response object to only set cookies/headers but still leave the rest to the framework), you must set the `passthrough` option to `true` in the `@Res({{ '{' }} passthrough: true {{ '}' }})` decorator. -->

> warning **Advertencia** Nest detecta cuando el controlador está usando `@Res()` o `@Next()`, lo que indica que ha elegido la opción específica de la libreria. Si se utilizan ambos enfoques al mismo tiempo, el enfoque estándar se **desactiva automáticamente** para esta ruta ya no funcionará como se espera. Para usar ambos enfoques al mismo tiempo (por ejemplo, al inyectar el objeto de respuesta para establecer solo cookies/encabezados pero dejar el resto al framework), debe establecer la opción `passthrough` en `true` en `@Res ({{ '{' }} passthrough: true {{ '}' }})` decorador.

<app-banner-devtools></app-banner-devtools>

<!-- #### Request object -->
#### EL objeto Request

<!-- Handlers often need access to the client **request** details. Nest provides access to the [request object](https://expressjs.com/en/api.html#req) of the underlying platform (Express by default). We can access the request object by instructing Nest to inject it by adding the `@Req()` decorator to the handler's signature. -->

Los controladores a menudo necesitan acceder a los detalles de la **solicitud/request** del cliente. Nest proporciona acceso al [objeto de solicitud/request object](https://expressjs.com/en/api.html#req) de la plataforma subyacente (Express por default). Podemos acceder al objeto de solicitud indicándole a Nest que lo inyecte agregando el decorador `@Req()` a la firma del controlador.

```typescript
@@filename(cats.controller)
import { Controller, Get, Req } from '@nestjs/common';
import { Request } from 'express';

@Controller('cats')
export class CatsController {
  @Get()
  findAll(@Req() request: Request): string {
    return 'This action returns all cats';
  }
}
@@switch
import { Controller, Bind, Get, Req } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Get()
  @Bind(Req())
  findAll(request) {
    return 'This action returns all cats';
  }
}
```

<!-- > info **Hint** In order to take advantage of `express` typings (as in the `request: Request` parameter example above), install `@types/express` package. -->
> info **Sugerencia** Para aprovechar la tipificación `express` (como en el ejemplo anterior del parámetro `request: Request`), instale el paquete `@types/express`.

<!-- The request object represents the HTTP request and has properties for the request query string, parameters, HTTP headers, and body (read more [here](https://expressjs.com/en/api.html#req)). In most cases, it's not necessary to grab these properties manually. We can use dedicated decorators instead, such as `@Body()` or `@Query()`, which are available out of the box. Below is a list of the provided decorators and the plain platform-specific objects they represent. -->
El objeto de solicitud representa la solicitud HTTP, tiene propiedades para la cadena de consulta de la solicitud, los parámetros, los encabezados HTTP y el cuerpo (lea más [aquí](https://expressjs.com/en/api.html#req)). En la mayoría de los casos, no es necesario tomar estas propiedades manualmente. En su lugar, podemos usar decoradores dedicados, como `@Body()` o `@Query()`, que están disponibles desde el primer momento. A continuación se muestra una lista de los decoradores provistos y los objetos simples específicos de la plataforma que representan.

<table>
  <tbody>
    <tr>
      <td><code>@Request(), @Req()</code></td>
      <td><code>req</code></td></tr>
    <tr>
      <td><code>@Response(), @Res()</code><span class="table-code-asterisk">*</span></td>
      <td><code>res</code></td>
    </tr>
    <tr>
      <td><code>@Next()</code></td>
      <td><code>next</code></td>
    </tr>
    <tr>
      <td><code>@Session()</code></td>
      <td><code>req.session</code></td>
    </tr>
    <tr>
      <td><code>@Param(key?: string)</code></td>
      <td><code>req.params</code> / <code>req.params[key]</code></td>
    </tr>
    <tr>
      <td><code>@Body(key?: string)</code></td>
      <td><code>req.body</code> / <code>req.body[key]</code></td>
    </tr>
    <tr>
      <td><code>@Query(key?: string)</code></td>
      <td><code>req.query</code> / <code>req.query[key]</code></td>
    </tr>
    <tr>
      <td><code>@Headers(name?: string)</code></td>
      <td><code>req.headers</code> / <code>req.headers[name]</code></td>
    </tr>
    <tr>
      <td><code>@Ip()</code></td>
      <td><code>req.ip</code></td>
    </tr>
    <tr>
      <td><code>@HostParam()</code></td>
      <td><code>req.hosts</code></td>
    </tr>
  </tbody>
</table>

<!-- <sup>\* </sup>For compatibility with typings across underlying HTTP platforms (e.g., Express and Fastify), Nest provides `@Res()` and `@Response()` decorators. `@Res()` is simply an alias for `@Response()`. Both directly expose the underlying native platform `response` object interface. When using them, you should also import the typings for the underlying library (e.g., `@types/express`) to take full advantage. Note that when you inject either `@Res()` or `@Response()` in a method handler, you put Nest into **Library-specific mode** for that handler, and you become responsible for managing the response. When doing so, you must issue some kind of response by making a call on the `response` object (e.g., `res.json(...)` or `res.send(...)`), or the HTTP server will hang. -->

<sup>\* </sup>Para la compatibilidad con los tipos en las plataformas HTTP subyacentes (por ejemplo, Express y Fastify), Nest proporciona decoradores `@Res()` y `@Response()`. `@Res()` es simplemente un alias para `@Response()`. Ambos exponen directamente la interfaz de objeto `respuesta` de la plataforma nativa subyacente. Al usarlos, también debe importar los tipos de la biblioteca subyacente (por ejemplo, `@types/express`) para aprovechar al máximo. Ten en cuenta que cuando inyectas `@Res()` o `@Response()` en un controlador de método, colocas a Nest en **modo específico de librería / Library-specific mode** para ese controlador y te vuelves responsable de administrar la respuesta. Al hacerlo, se debe emitir algún tipo de respuesta haciendo una llamada al objeto `response` (por ejemplo, `res.json(...)` o `res.send(...)`), o el servidor se colgará.

<!-- > info **Hint** To learn how to create your own custom decorators, visit [this](/custom-decorators) chapter. -->
> información **Sugerencia** Para aprender a crear sus propios decoradores personalizados, visite [este](/decoradores-personalizados) capítulo .

<!-- #### Resources -->
#### Rescursos


<!-- Earlier, we defined an endpoint to fetch the cats resource (**GET** route). We'll typically also want to provide an endpoint that creates new records. For this, let's create the **POST** handler: -->

Anteriormente, definimos un endpoint para obtener el recurso cats (ruta **GET**). Por lo general, también querremos proporcionar un endpoint que cree nuevos registros. Para esto, vamos a crear el manejador **POST**:

```typescript
@@filename(cats.controller)
import { Controller, Get, Post } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Post()
  create(): string {
    return 'This action adds a new cat';
  }

  @Get()
  findAll(): string {
    return 'This action returns all cats';
  }
}
@@switch
import { Controller, Get, Post } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Post()
  create() {
    return 'This action adds a new cat';
  }

  @Get()
  findAll() {
    return 'This action returns all cats';
  }
}
```

<!-- It's that simple. Nest provides decorators for all of the standard HTTP methods: `@Get()`, `@Post()`, `@Put()`, `@Delete()`, `@Patch()`, `@Options()`, and `@Head()`. In addition, `@All()` defines an endpoint that handles all of them. -->
Es así de simple. Nest proporciona decoradores para todos los métodos HTTP estándar: `@Get()`, `@Post()`, `@Put()`, `@Delete()`, `@Patch()`, `@Options( )` y `@Head()`. Además, `@All()` define un endpoint que los maneja a todos.

<!-- #### Route wildcards -->
### Comodines para las Rutas

<!-- Pattern based routes are supported as well. For instance, the asterisk is used as a wildcard, and will match any combination of characters. -->
Las rutas basadas en patrones también son compatibles. Por ejemplo, el asterisco se usa como comodín y coincidirá con cualquier combinación de caracteres.

```typescript
@Get('ab*cd')
findAll() {
  return 'This route uses a wildcard';
}
```

<!-- The `'ab*cd'` route path will match `abcd`, `ab_cd`, `abecd`, and so on. The characters `?`, `+`, `*`, and `()` may be used in a route path, and are subsets of their regular expression counterparts. The hyphen ( `-`) and the dot (`.`) are interpreted literally by string-based paths. -->

La ruta de la ruta `'ab*cd'` coincidirá con `abcd`, `ab_cd`, `abecd`, etc. Los caracteres `?`, `+`, `*` y `()` se pueden usar en una ruta de ruta y son subconjuntos de sus equivalentes de expresiones regulares. El guión (`-`) y el punto (`.`) se interpretan literalmente mediante rutas basadas en cadenas.

<!-- #### Status code -->
#### Código de estado / Status code
<!-- As mentioned, the response **status code** is always **200** by default, except for POST requests which are **201**. We can easily change this behavior by adding the `@HttpCode(...)` decorator at a handler level. -->

Como se mencionó, el **status code** de respuesta siempre es **200** de manera predeterminada, excepto para las solicitudes POST que son **201**. Podemos cambiar fácilmente este comportamiento agregando el decorador `@HttpCode(...)` en un nivel de controlador.

```typescript
@Post()
@HttpCode(204)
create() {
  return 'This action adds a new cat';
}
```

<!-- > info **Hint** Import `HttpCode` from the `@nestjs/common` package. -->
> info **Sugerencia** Importa `HttpCode` desde el paquete `@nestjs/common`.

Often, your status code isn't static but depends on various factors. In that case, you can use a library-specific **response** (inject using `@Res()`) object (or, in case of an error, throw an exception).

<!-- #### Headers -->
#### Encabezados / Headers

<!-- To specify a custom response header, you can either use a `@Header()` decorator or a library-specific response object (and call `res.header()` directly). -->
Para especificar un encabezado de respuesta personalizado, puede usar un decorador `@Header()` o un objeto de respuesta específico de la libreria (y llamar a `res.header()` directamente).

```typescript
@Post()
@Header('Cache-Control', 'none')
create() {
  return 'This action adds a new cat';
}
```

<!-- > info **Hint** Import `Header` from the `@nestjs/common` package. -->
> info **Sugerencia** Importa `Header` desde el paquete `@nestjs/common`.

<!-- #### Redirection -->
#### Redireccionamiento


<!-- To redirect a response to a specific URL, you can either use a `@Redirect()` decorator or a library-specific response object (and call `res.redirect()` directly). -->
Para redirigir una respuesta a una URL específica, puede usar un decorador `@Redirect()` o un objeto de respuesta específico de la biblioteca (y llamar a `res.redirect()` directamente).

<!-- `@Redirect()` takes two arguments, `url` and `statusCode`, both are optional. The default value of `statusCode` is `302` (`Found`) if omitted. -->
`@Redirect()` toma dos argumentos, `url` y `statusCode`, ambos son opcionales. El valor predeterminado de `statusCode` es `302` (`Found`) si se omite.

```typescript
@Get()
@Redirect('https://nestjs.com', 301)
```

<!-- Sometimes you may want to determine the HTTP status code or the redirect URL dynamically. Do this by returning an object from the route handler method with the shape: -->
A veces, es posible que desee determinar el código de estado HTTP o la URL de redirección de forma dinámica. Haga esto devolviendo un objeto del método del controlador de ruta con la forma:

```json
{
  "url": string,
  "statusCode": number
}
```

<!-- Returned values will override any arguments passed to the `@Redirect()` decorator. For example: -->
Los valores devueltos anularán cualquier argumento pasado al decorador `@Redirect()`. Por ejemplo:

```typescript
@Get('docs')
@Redirect('https://docs.nestjs.com', 302)
getDocs(@Query('version') version) {
  if (version && version === '5') {
    return { url: 'https://docs.nestjs.com/v5/' };
  }
}
```

<!-- #### Route parameters -->
#### parámetrode de Rutas / Route parameters

<!-- Routes with static paths won't work when you need to accept **dynamic data** as part of the request (e.g., `GET /cats/1` to get cat with id `1`). In order to define routes with parameters, we can add route parameter **tokens** in the path of the route to capture the dynamic value at that position in the request URL. The route parameter token in the `@Get()` decorator example below demonstrates this usage. Route parameters declared in this way can be accessed using the `@Param()` decorator, which should be added to the method signature. -->

Las rutas con rutas estáticas no funcionarán cuando necesite aceptar **datos dinámicos** como parte de la solicitud (por ejemplo, `GET /cats/1` para obtener 'cat' con id `1`). Para definir rutas con parámetros, podemos agregar **tokens** al parámetros de ruta  para capturar el valor dinámico en esa posición en la URL de solicitud. El token del parámetro de ruta en el ejemplo del decorador `@Get()` a continuación demuestra este uso. Se puede acceder a los parámetros de ruta declarados de esta manera utilizando el decorador `@Param()`, que debe agregarse a la firma del método.

<!-- > info **Hint** Routes with parameters should be declared after any static paths. This prevents the parameterized paths from intercepting traffic destined for the static paths. -->
> info **Sugerencia** Las rutas con parámetros deben declararse después de cualquier ruta estática. Esto evita que las rutas parametrizadas intercepten el tráfico destinado a las rutas estáticas.

```typescript
@@filename()
@Get(':id')
findOne(@Param() params: any): string {
  console.log(params.id);
  return `This action returns a #${params.id} cat`;
}
@@switch
@Get(':id')
@Bind(Param())
findOne(params) {
  console.log(params.id);
  return `This action returns a #${params.id} cat`;
}
```

<!-- `@Param()` is used to decorate a method parameter (`params` in the example above), and makes the **route** parameters available as properties of that decorated method parameter inside the body of the method. As seen in the code above, we can access the `id` parameter by referencing `params.id`. You can also pass in a particular parameter token to the decorator, and then reference the route parameter directly by name in the method body. -->

`@Param()` se usa para decorar un parámetro de método (`params` en el ejemplo anterior), y hace que los parámetros de **ruta** estén disponibles como propiedades de ese parámetro de método decorado dentro del cuerpo del método. Como se ve en el código anterior, podemos acceder al parámetro `id` haciendo referencia a `params.id`. También puede pasar un token de parámetro particular al decorador y luego hacer referencia al parámetro de ruta directamente por nombre en el cuerpo del método.

> info **Hint** Import `Param` from the `@nestjs/common` package.

```typescript
@@filename()
@Get(':id')
findOne(@Param('id') id: string): string {
  return `This action returns a #${id} cat`;
}
@@switch
@Get(':id')
@Bind(Param('id'))
findOne(id) {
  return `This action returns a #${id} cat`;
}
```

<!-- #### Sub-Domain Routing -->
#### Enrutamiento de subdominio / Sub-Domain Routing

<!-- The `@Controller` decorator can take a `host` option to require that the HTTP host of the incoming requests matches some specific value. -->

El decorador `@Controller` puede tomar una opción `host` para requerir que el host HTTP de las solicitudes entrantes coincida con algún valor específico.

```typescript
@Controller({ host: 'admin.example.com' })
export class AdminController {
  @Get()
  index(): string {
    return 'Admin page';
  }
}
```

<!-- > **Warning** Since **Fastify** lacks support for nested routers, when using sub-domain routing, the (default) Express adapter should be used instead. -->

> **Warning** Dado que **Fastify** no es compatible con los enrutadores anidados, al usar el enrutamiento de subdominio, se debe usar el adaptador Express (predeterminado).

Similar to a route `path`, the `hosts` option can use tokens to capture the dynamic value at that position in the host name. The host parameter token in the `@Controller()` decorator example below demonstrates this usage. Host parameters declared in this way can be accessed using the `@HostParam()` decorator, which should be added to the method signature.

De manera similar a un `path` de ruta, la opción `hosts` puede usar tokens para capturar el valor dinámico en esa posición en el nombre del host. El token de parámetro de host en el ejemplo del decorador `@Controller()` a continuación demuestra este uso. Se puede acceder a los parámetros de host declarados de esta manera utilizando el decorador `@HostParam()`, que debe agregarse a la firma del método.

```typescript
@Controller({ host: ':account.example.com' })
export class AccountController {
  @Get()
  getInfo(@HostParam('account') account: string) {
    return account;
  }
}
```

<!-- #### Scopes -->
#### Ámbito - Alcance / Scope

<!-- For people coming from different programming language backgrounds, it might be unexpected to learn that in Nest, almost everything is shared across incoming requests. We have a connection pool to the database, singleton services with global state, etc. Remember that Node.js doesn't follow the request/response Multi-Threaded Stateless Model in which every request is processed by a separate thread. Hence, using singleton instances is fully **safe** for our applications. -->

Para las personas que provienen de diferentes entornos de lenguaje de programación, puede ser inesperado saber que en Nest, casi todo se comparte entre las solicitudes entrantes. Tenemos un grupo de conexiones a la base de datos, servicios singleton con estado global, etc. Recuerde que Node.js no sigue el modelo sin estado de subprocesos múltiples de solicitud/respuesta en el que cada solicitud es procesada por un subproceso separado. Por lo tanto, usar instancias singleton es completamente **seguro** para nuestras aplicaciones.

<!-- However, there are edge-cases when request-based lifetime of the controller may be the desired behavior, for instance per-request caching in GraphQL applications, request tracking or multi-tenancy. Learn how to control scopes [here](/fundamentals/injection-scopes). -->

Sin embargo, hay casos extremos en los que la vida útil del controlador basada en solicitudes puede ser el comportamiento deseado, por ejemplo, almacenamiento en caché por solicitud en aplicaciones GraphQL, seguimiento de solicitudes o tenencia múltiple. Aprenda a controlar los alcances(scopes) [aquí](/fundamentals/injection-scopes).

<!-- #### Asynchronicity -->
#### Asincronía / Asynchronicity

<!-- We love modern JavaScript and we know that data extraction is mostly **asynchronous**. That's why Nest supports and works well with `async` functions. -->
Nos encanta el JavaScript moderno y sabemos que la extracción de datos es principalmente **asincrónica**. Es por eso que Nest admite y funciona bien con las funciones `asincrónicas`.

> info **Hint** Learn more about `async / await` feature [here](https://kamilmysliwiec.com/typescript-2-1-introduction-async-await)

Every async function has to return a `Promise`. This means that you can return a deferred value that Nest will be able to resolve by itself. Let's see an example of this:

```typescript
@@filename(cats.controller)
@Get()
async findAll(): Promise<any[]> {
  return [];
}
@@switch
@Get()
async findAll() {
  return [];
}
```

The above code is fully valid. Furthermore, Nest route handlers are even more powerful by being able to return RxJS [observable streams](http://reactivex.io/rxjs/class/es6/Observable.js~Observable.html). Nest will automatically subscribe to the source underneath and take the last emitted value (once the stream is completed).

```typescript
@@filename(cats.controller)
@Get()
findAll(): Observable<any[]> {
  return of([]);
}
@@switch
@Get()
findAll() {
  return of([]);
}
```

Both of the above approaches work and you can use whatever fits your requirements.

#### Request payloads

Our previous example of the POST route handler didn't accept any client params. Let's fix this by adding the `@Body()` decorator here.

But first (if you use TypeScript), we need to determine the **DTO** (Data Transfer Object) schema. A DTO is an object that defines how the data will be sent over the network. We could determine the DTO schema by using **TypeScript** interfaces, or by simple classes. Interestingly, we recommend using **classes** here. Why? Classes are part of the JavaScript ES6 standard, and therefore they are preserved as real entities in the compiled JavaScript. On the other hand, since TypeScript interfaces are removed during the transpilation, Nest can't refer to them at runtime. This is important because features such as **Pipes** enable additional possibilities when they have access to the metatype of the variable at runtime.

Let's create the `CreateCatDto` class:

```typescript
@@filename(create-cat.dto)
export class CreateCatDto {
  name: string;
  age: number;
  breed: string;
}
```

It has only three basic properties. Thereafter we can use the newly created DTO inside the `CatsController`:

```typescript
@@filename(cats.controller)
@Post()
async create(@Body() createCatDto: CreateCatDto) {
  return 'This action adds a new cat';
}
@@switch
@Post()
@Bind(Body())
async create(createCatDto) {
  return 'This action adds a new cat';
}
```

> info **Hint** Our `ValidationPipe` can filter out properties that should not be received by the method handler. In this case, we can whitelist the acceptable properties, and any property not included in the whitelist is automatically stripped from the resulting object. In the `CreateCatDto` example, our whitelist is the `name`, `age`, and `breed` properties. Learn more [here](https://docs.nestjs.com/techniques/validation#stripping-properties).

#### Handling errors

There's a separate chapter about handling errors (i.e., working with exceptions) [here](/exception-filters).

#### Full resource sample

Below is an example that makes use of several of the available decorators to create a basic controller. This controller exposes a couple of methods to access and manipulate internal data.

```typescript
@@filename(cats.controller)
import { Controller, Get, Query, Post, Body, Put, Param, Delete } from '@nestjs/common';
import { CreateCatDto, UpdateCatDto, ListAllEntities } from './dto';

@Controller('cats')
export class CatsController {
  @Post()
  create(@Body() createCatDto: CreateCatDto) {
    return 'This action adds a new cat';
  }

  @Get()
  findAll(@Query() query: ListAllEntities) {
    return `This action returns all cats (limit: ${query.limit} items)`;
  }

  @Get(':id')
  findOne(@Param('id') id: string) {
    return `This action returns a #${id} cat`;
  }

  @Put(':id')
  update(@Param('id') id: string, @Body() updateCatDto: UpdateCatDto) {
    return `This action updates a #${id} cat`;
  }

  @Delete(':id')
  remove(@Param('id') id: string) {
    return `This action removes a #${id} cat`;
  }
}
@@switch
import { Controller, Get, Query, Post, Body, Put, Param, Delete, Bind } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Post()
  @Bind(Body())
  create(createCatDto) {
    return 'This action adds a new cat';
  }

  @Get()
  @Bind(Query())
  findAll(query) {
    console.log(query);
    return `This action returns all cats (limit: ${query.limit} items)`;
  }

  @Get(':id')
  @Bind(Param('id'))
  findOne(id) {
    return `This action returns a #${id} cat`;
  }

  @Put(':id')
  @Bind(Param('id'), Body())
  update(id, updateCatDto) {
    return `This action updates a #${id} cat`;
  }

  @Delete(':id')
  @Bind(Param('id'))
  remove(id) {
    return `This action removes a #${id} cat`;
  }
}
```

> info **Hint** Nest CLI provides a generator (schematic) that automatically generates **all the boilerplate code** to help us avoid doing all of this, and make the developer experience much simpler. Read more about this feature [here](/recipes/crud-generator).

#### Getting up and running

With the above controller fully defined, Nest still doesn't know that `CatsController` exists and as a result won't create an instance of this class.

Controllers always belong to a module, which is why we include the `controllers` array within the `@Module()` decorator. Since we haven't yet defined any other modules except the root `AppModule`, we'll use that to introduce the `CatsController`:

```typescript
@@filename(app.module)
import { Module } from '@nestjs/common';
import { CatsController } from './cats/cats.controller';

@Module({
  controllers: [CatsController],
})
export class AppModule {}
```

We attached the metadata to the module class using the `@Module()` decorator, and Nest can now easily reflect which controllers have to be mounted.

<app-banner-shop></app-banner-shop>

#### Library-specific approach

So far we've discussed the Nest standard way of manipulating responses. The second way of manipulating the response is to use a library-specific [response object](https://expressjs.com/en/api.html#res). In order to inject a particular response object, we need to use the `@Res()` decorator. To show the differences, let's rewrite the `CatsController` to the following:

```typescript
@@filename()
import { Controller, Get, Post, Res, HttpStatus } from '@nestjs/common';
import { Response } from 'express';

@Controller('cats')
export class CatsController {
  @Post()
  create(@Res() res: Response) {
    res.status(HttpStatus.CREATED).send();
  }

  @Get()
  findAll(@Res() res: Response) {
     res.status(HttpStatus.OK).json([]);
  }
}
@@switch
import { Controller, Get, Post, Bind, Res, Body, HttpStatus } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Post()
  @Bind(Res(), Body())
  create(res, createCatDto) {
    res.status(HttpStatus.CREATED).send();
  }

  @Get()
  @Bind(Res())
  findAll(res) {
     res.status(HttpStatus.OK).json([]);
  }
}
```

Though this approach works, and does in fact allow for more flexibility in some ways by providing full control of the response object (headers manipulation, library-specific features, and so on), it should be used with care. In general, the approach is much less clear and does have some disadvantages. The main disadvantage is that your code becomes platform-dependent (as underlying libraries may have different APIs on the response object), and harder to test (you'll have to mock the response object, etc.).

Also, in the example above, you lose compatibility with Nest features that depend on Nest standard response handling, such as Interceptors and `@HttpCode()` / `@Header()` decorators. To fix this, you can set the `passthrough` option to `true`, as follows:

```typescript
@@filename()
@Get()
findAll(@Res({ passthrough: true }) res: Response) {
  res.status(HttpStatus.OK);
  return [];
}
@@switch
@Get()
@Bind(Res({ passthrough: true }))
findAll(res) {
  res.status(HttpStatus.OK);
  return [];
}
```

Now you can interact with the native response object (for example, set cookies or headers depending on certain conditions), but leave the rest to the framework.
