# Tutorial Meteor 1.3

El 28 de Marzo de 2016 se liberó la versión 1.3 de Meteor con importantes cambios en la forma de generar apps, pero retro-compatible con versiones anteriores, por fortuna.

Los principales cambios que trae son:
- Integración con ReactJS y Angular2 para la vista (View) a parte del ya conocido sistema de plantillas Blaze.
- Integración con ECMAScript 6, siendo lo mas notable el uso de import y export.
- Integración mejorada con el gestor de paquetes de node, npm: meteor npm install PAQUETE_NPM
- Mejora del sistema de reactividad de variables con ReactiveVar, sustituyendo a Session, aunque se puede seguir usando, eso sí, añadiendo el paquete a nuestro proyecto.
- Mejora del sistema de testing
- Mejora de la implementación con Cordova, actualizada a la versión 6.0.0, Cordova iOS 4.1.0 y Android 5.1.1
- Mejora del rendimiento en la generación de la aplicación y en ciertas queries deupdate de minimongo

# Creación de un proyecto

A la hora de crear un proyecto nada cambia:

```javascript
  $ meteor create tutorial-meteor-1.3
  $ cd intermediate
  $ meteor
```

Arrancando el servidor en localhost:3000 y mostrando la app de "Welcome to Meteor" y el botón que incrementa un contador.

Desde la consola, podremos ver que ya no es posible acceder a Session.get("counter"), como ocurria en la versión 1.2
Ahora para incrementar el contador y que se propague su valor a la plantilla (data-binding) utiliza ReactiveVar, la cual es accesible desde la Template, pero no desde la consola, por lo que ahora encapsula esos valores y nadie puede manipularlos.

#Estructura inicial de directorios

Cuando creamos el proyecto podemos ver que nos crea la siguiente estructura de directorios:
```bash
  tutorial-meteor-1.3
    |____ client
            |___ main.css
            |___ main.html
            |___ main.js
    |____ server
            |___ main.js
    |____ .meteor
    |____ package.json
```
En el directorio ```client``` tenemos los 3 tipos de ficheros que necesitaremos como mínimo para la parte del front-end o cliente.
En el directorio ```server``` tenemos el script que se ejecutará en servidor, que como vemos es javascript.
El directorio .meteor lo usa Meteor mientras desarrollamos y no debemos de tocar ahí.
El fichero package.json donde ira la configuración de los paquetes de node (npm) que vayamos necesitando e instalando.

#Comentando el código del ejemplo inicial

Podemos ver que en ```server/main.js``` no tenemos código para ejecutar, solo lo mínimo que necesitaremos para agregarlo nosotros:
```javascript
import { Meteor } from 'meteor/meteor';

Meteor.startup(() => {
  // code to run on server at startup
});
```
Podemos ver que ```Meteor 1.3``` usa ECMAScript 6 y podemos hacer uso de los import y export que incorpora.
Además podemos usarlo sin preocuparnos de instalar un transpilers (source-to-source-compiler), es decir, un programa que nos traduzca ES6 a ES5 para que funcione siempre donde se ejecute, ya que aún ES6 está en fase de implantación.

En ```client/main.html``` tenemos el HTML del ejemplo. Podemos ver que solo declara el tag ```head``` y el tag ```body```
que Meteor se encargará de añadir dentro del ```<html>```. Luego tenemos el tag ```template```. En este caso solo 1, pero podemos tener todos los tag ```template``` que necesitemos, anidandolos como necesitemos, para separar las partes de nuestra app.

```javascript
  <head>
    <title>simple</title>
  </head>

  <body>
    <h1>Welcome to Meteor!</h1>

    {{> hello}}
  </body>

  <template name="hello">
    <button>Click Me</button>
    <p>You`ve pressed the button {{counter}} times.</p>
  </template>
```

En ```client/main.js``` tenemos el código relacionado con la plantilla principal.



#Step 1 - Cambiando un poco el ejemplo inicial

Vamos a añadir a nuestro ejemplo otra variable que dependa de la variable contador. Editamos primero
