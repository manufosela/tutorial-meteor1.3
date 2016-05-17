# Tutorial Meteor 1.3

El 28 de Marzo de 2016 se liberó la versión 1.3 de Meteor con importantes cambios en la forma de generar apps, pero retro-compatible con versiones anteriores, por fortuna.

Los principales cambios que trae son:
- Selección del manejo de la vista (View) que a parte de su ya conocido sistema de plantillas Blaze, ahora además se integra a la perfección con ReactJS y Angular2 .
- Integración con ECMAScript 6, siendo lo mas notable el uso de import y export y las arrow functions (=>).
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
Como diferencia respecto a versiones anteriores ```Meteor 1.3``` usa ECMAScript 6 y por eso podemos hacer uso de ```import``` y ```export```, como el import que podemos ver en el código de server/main.js.

Además podemos usar ES6 sin preocuparnos de instalar un transpilers (source-to-source-compiler), es decir, un programa que nos traduzca ES6 a ES5 para que funcione siempre hayá donde se ejecute, ya que aún ES6 está en fase de implantación.

En ```client/main.html``` tenemos el HTML del ejemplo. Podemos ver que solo declara el tag ```head``` y el tag ```body```
que Meteor se encargará de añadir dentro del ```<html>```.
Luego tenemos el tag ```template```. En este caso solo 1, pero podemos tener todos los tag ```template``` que necesitemos, anidandolos como necesitemos, para separar las partes de nuestra app.

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
La plantilla debe llevar obligatoriamente un atributo ```name``` para identificarla. Dicho identificador debe ser único en nuestra app.

La forma de invocar al template es usando los ```"mostachos" {{ }}``` y dentro el signo ```>``` junto al nombre de la plantilla que queremos que se interprete (render): ```{{> hello}}```

Igualmente en la plantilla (template) podemos ver que dentro también se usan los ```"mostachos"```, esta vez sin >, para invocar a una variable, que después declararemos en el ```método "helper"``` de la plantilla: ```{{counter}}```
Por defecto Meteor sigue usando su sistema de plantillas llamado ```Blaze``` (basado en ```handlebars```) aunque, como veremos mas adelante, en esta versión permite sustituirlo por ReactJS o por Angular2.

En ```client/main.js``` tenemos el código con el control y funcionamiento relacionado con la plantilla principal.
```javascript
  import { Template } from 'meteor/templating';
  import { ReactiveVar } from 'meteor/reactive-var';

  import './main.html';

  Template.hello.onCreated(function helloOnCreated() {
    // counter starts at 0
    this.counter = new ReactiveVar(0);
  });

  Template.hello.helpers({
    counter() {
      return Template.instance().counter.get();
    }
  });

  Template.hello.events({
    'click button'(event, instance) {
      // increment the counter when button is clicked
      instance.counter.set(instance.counter.get() + 1);
    },
  });
```

En el código javascript cliente es donde vemos bastantes cambios respecto a versiones anteriores.

  1. Antes para el contador se usaban sesiones con el objeto ```Session```, el cual implementa los métodos ```get``` y ```set``` para recuperar o inicializar variables dentro de dicho objeto y que está en la parte cliente. Ahora prescinde del objeto ```Session``` usado como "data-binding", el cual era accesible desde consola y por lo tanto modificable y lo sustituye por el objeto ReactiveVar accesible solamente desde el objeto Template, quedando así acotado su acceso desde el lado cliente, evitando que nadie pueda leer o modificar esas variables. Aún asi el objeto Session se puede usar añadiendo el paquete al proyecto: ```meteor add session```
  2. Ahora es necesario especificar los import necesarios como el paquete ```templating``` y el ```reactive-var```. Asi mismo importamos el fichero main.html
  3. EL objeto ```Template.hello``` tiene 3 métodos:
  
     3.1 El método ```helpers```, el cual recibe un objeto donde se define el comportamiento y lo qué devuelve cada variable de la plantilla.
     
      3.2 El método ```events```, el cual recibe un objeto donde se definen los eventos asociados a la plantilla.
      
      3.3 El método ```onCreated```, el cual recibe una función que se ejecuta cuando la plantilla ha sido creada. 
      
  4. Se simplifica la sintaxis de definición de los ```helpers```:
    ```javascript
      Template.body.helpers({
        todos() {
          return Todos.find();
        }
      });
    ```
    En vez de como se hacia hasta la versión 1.2:
    ```javascript
      Template.body.helpers({
        todos: function todos(){
          return Todos.find();
        }
      });
    ```
    
#Step 1 - Cambiando un poco el ejemplo inicial

Vamos a añadir a nuestro ejemplo otra variable que dependa de la variable contador. Editamos primero el fichero ```client/main.js```. En el método ```helpers```:

```javascript
  Template.hello.helpers({
    counter() {
      return Template.instance().counter.get();
    },
    clickmetext() {
      return "click me to " + (Template.instance().counter.get() + 1);
    }
  });
```

Observa como se accede ahora a la variable reactiva ```counter``` accediendo a la instancia del objeto Template y usando el método ```get()``` para recuerar su valor.
Igualmente para inicializar o cambiar el valor de dicha variable se usa el método ```set(valor)```

Una vez añadido el método al objeto ```helpers``` modificamos el fichero ```main.html``` para usarlo, sustituyendo el texto "Click me" por la variable ```{{clickmetext}}```:

```html
  <template name="hello">
    <button>{{clickmetext}}</button>
    <p>You've pressed the button {{counter}} times.</p>
  </template>
```

Según guardemos veremos que la app se actualiza sin tener que recargar la página, ya que ```Meteor``` sigue detectando automaticamente los cambios en el código, refrescando automaticamente cuando es necesario.

# Step 2 - Añadiendo también el objeto Session


# Step 3 - El mismo ejemplo con ReactJS

# Step 4 - El mismo ejemplo con Angular 2

# Step 5 - La nueva forma de estructurar una App

# Step 6 - Collections y ReactJS
