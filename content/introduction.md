<!-- ### Introduction -->
### Introducción

<!-- Nest (NestJS) is a framework for building efficient, scalable [Node.js](https://nodejs.org/) server-side applications. It uses progressive JavaScript, is built with and fully supports [TypeScript](http://www.typescriptlang.org/) (yet still enables developers to code in pure JavaScript) and combines elements of OOP (Object Oriented Programming), FP (Functional Programming), and FRP (Functional Reactive Programming). -->

Nest (NestJS) es un framework para el desarrollo eficiente, escalable [Node.js](https://nodejs.org/) de aplicaciones del lado del servidor. Utiliza "JavaScript Progresivo / Avanzado" (progressive JavaScript), esta contruido y soporta [TypeScript](http://www.typescriptlang.org/) (sin embargo permite a los desarrolladores crear código en JavaScript puro) y combina elementos de la "OOP / POO" (Object Oriented Programming / Programación Orientada a Objetos), "FP / PF" (Functional Programming / Programación Funcional), y "FRP / PFR" (Functional Reactive Programming / Programación Funcional Reactiva).

<!-- Under the hood, Nest makes use of robust HTTP Server frameworks like [Express](https://expressjs.com/) (the default) and optionally can be configured to use [Fastify](https://github.com/fastify/fastify) as well! -->

En el fondo (Under the hood), Nest hace uso de frameworks de Servidor HTTP robustos como [Express](https://expressjs.com/) (por default) y opcionalmente se puede configurar para usar [Fastify](https://github.com/fastify/fastify) tambien!

<!-- Nest provides a level of abstraction above these common Node.js frameworks (Express/Fastify), but also exposes their APIs directly to the developer. This gives developers the freedom to use the myriad of third-party modules which are available for the underlying platform. -->

Nest provee un nivel de abstracción sobre todos estos frameworks de Node.js (Express/Fastify), pero también expone su API directamente al desarrollador. Esto da a los desarrolladores la libertad para usar los númerosos modulos de terceros que se encuentran disponibles para la plataforma subyacente.

<!-- #### Philosophy -->
#### Filosofía

<!-- In recent years, thanks to Node.js, JavaScript has become the “lingua franca” of the web for both front and backend applications. This has given rise to awesome projects like [Angular](https://angular.io/), [React](https://github.com/facebook/react) and [Vue](https://github.com/vuejs/vue), which improve developer productivity and enable the creation of fast, testable, and extensible frontend applications. However, while plenty of superb libraries, helpers, and tools exist for Node (and server-side JavaScript), none of them effectively solve the main problem of - **Architecture**. -->

En años recientes, gracias a Node.js, JavaScript se ha convertido en la "lingua franca" de la web tanto para el frontend como para el backend de las aplicaciones. Esto ha dado pie a asombrosos proyectos como [Angular](https://angular.io/), [React](https://github.com/facebook/react) y [Vue](https://github.com/vuejs/vue), que mejoran la productividad del desarrollador y permite la creación de aplicaciones frontend que son: rápidas, testeables y extensibles. Si embargo,  mientras que un montón de magníficas bibliotecas, ayudantes y herramientas existen para Node.js (y server-side JavaScript), ninguna de ellas a resuelto de manera efectiva el problema de la **Arquitectura**.

<!-- Nest provides an out-of-the-box application architecture which allows developers and teams to create highly testable, scalable, loosely coupled, and easily maintainable applications. The architecture is heavily inspired by Angular. -->

Nest proporciona una arquitectura lista para usar, que permite a los desarrolladores y equipos crear aplicaciones altamente testeables, escalables, débilmente acopladas y fáciles de mantener. La arquitectura está fuertemente inspirada en Angular. 

<!-- #### Installation -->
#### Instalación

<!-- To get started, you can either scaffold the project with the [Nest CLI](/cli/overview), or clone a starter project (both will produce the same outcome). -->
Para comenzar, puedes inciar un proyecto con una plantilla inicial usando la [Nest CLI](/cli/overview), o clonar un proyecto inicial (ambos producen el mismo resultado).

<!-- To scaffold the project with the Nest CLI, run the following commands. This will create a new project directory, and populate the directory with the initial core Nest files and supporting modules, creating a conventional base structure for your project. Creating a new project with the **Nest CLI** is recommended for first-time users. We'll continue with this approach in [First Steps](first-steps). -->

Para inciar el proyecto con la plantilla usando la Nest CLI, necesitas los siguientes comandos. Esto creará un nuevo proyecto en el directorio, y lo llenará con los archivos de inicio escenciales de Nest y los modulos que los soportan. Creando una estructura base convencional para tu proyecto. Crear un nuevo proyecto con el **Nest CLI** es recomendado para los novatos. Continuaremos con este adcercamiento en los  [Primeros Pasos](first-steps).

```bash
$ npm i -g @nestjs/cli
$ nest new project-name
```

<!-- > info **Hint** To create a new project with TypeScript's [strict](https://www.typescriptlang.org/tsconfig#strict) mode enabled, pass the `--strict` flag to the `nest new` command.  -->
> info **Pista** Para crear un nuevo proyecto con TypeScript's [strict](https://www.typescriptlang.org/tsconfig#strict) modo activo, coloca la `--strict` flag al comando `nest new`.

<!-- #### Alternatives -->
#### Alternativas

<!-- Alternatively, to install the TypeScript starter project with **Git**: -->
Alternativamente, puedes instalar TypeScript al proyecto inicial con **Git**:

```bash
$ git clone https://github.com/nestjs/typescript-starter.git project
$ cd project
$ npm install
$ npm run start
```

<!-- > info **Hint** If you'd like to clone the repository without the git history, you can use [degit](https://github.com/Rich-Harris/degit). -->
> info **Pista** Si lo que quieres es clonar un repositorio sin el historial de git, puedes usar [degit](https://github.com/Rich-Harris/degit).

<!-- Open your browser and navigate to [`http://localhost:3000/`](http://localhost:3000/). -->
Abre tu navegador y ve a  [`http://localhost:3000/`](http://localhost:3000/).

<!-- To install the JavaScript flavor of the starter project, use `javascript-starter.git` in the command sequence above. -->
Para instalar la version de JavaScript en el proyecto inicial usa `javascript-starter.git` en la secuencia de comando de arriba.

<!-- You can also manually create a new project from scratch by installing the core and supporting files with **npm** (or **yarn**). In this case, of course, you'll be responsible for creating the project boilerplate files yourself. -->
También pudes crear un nuevo proyecto desde cero haciendo uso de lo esencial y los archivos de soporte con **npm** (o **yarn**). 
Por supuesto, en este caso, tú serás el resposable de cear el proyecto y su estructura inicial.

```bash
$ npm i --save @nestjs/core @nestjs/common rxjs reflect-metadata
```
