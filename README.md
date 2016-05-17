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
  $ meteor create intermediate
  $ cd intermediate
  $ meteor
```

Arrancando el servidor en localhost:3000 y mostrando la app de "Welcome to Meteor" y el botón que incrementa un contador.

Desde la consola, podremos ver que ya no es posible acceder a Session.get("counter"), como ocurria en la versión 1.2
Ahora para incrementar el contador y que se propague su valor a la plantilla (data-binding) utiliza ReactiveVar, la cual es accesible desde la Template, pero no desde la consola, por lo que ahora encapsula esos valores y nadie puede manipularlos.

