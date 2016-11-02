https://angular.io/docs/ts/latest/guide/upgrade.html

# ACTUALIZANDO Angular 1.X

### Actualizacion paso a paso de una aplicacion Angular 1 hacia Angular 2

Disponer de una aplicacion desarrollada con Angular 1 no implica que no 
podamos hacer uso de los beneficios que puede ofrecer Angular 2. Angular 2 
posee herramientas para migrar una aplicacion Angular 1 hacia la plataforma
Angular 2.

Tendremos aplicaciones mas sencillas de migrar, y hay procedimientos que 
podemos usar para facilitar el trabajo. Es posible preparar una aplicacion
desarrollada con Angular 1 para migrar a Angular 2, este proceso puede 
hacerse antes de empezar el proceso de migracion. Este proceso de preparado
tiene como finalidad tener codigo mas independiente y desacoplado, mas 
mantenible y preparado para las herramientas de desarrollo actuales. Esta
preparacion presentara no solo el beneficio de una actualizacion mas sencilla,
sino que tambien generara un mejoramiento de nuestra aplicacion Angular 1.

La clave para lograr una migracion satisfactoria es hacerlo de forma 
incremental, ejecutando los dos frameworks de forma paralela, migrando 
componente a componente, uno por vez. Esto nos habilita a migrar aplicaciones
de derentes tamaños y complejidad, tambien para aplicaciones grandes, evitando 
interrupciones en quienes consumen estas aplicaciones, dado que este trabajo
puede hacerse de forma colaborativa y en un periodo de tiempo. El modulo 
`upgrade` en Angular 2 ha sido diseñado para llevar adelante actualizaciones 
incrementales.

1. [Preparacion](#preparacion)
  1. [Usando la guia de estilos de Angular](#usando-la-guia-de-estilos-de-angular)
  2. [Usando un `Module Loader`](#usando-un-module-loader)
  3. [Migrando a Typescript](#migrando-a-typescript)
  3. [Usando `Component Directives`](#usando-component-directives)
2. [Actualizando con el `Upgrade Adapter`](#atualizando-con-el-upgrade-adapter)
  1. [Como funciona el `Upgrade Adapter`](#como-funciona-el-upgrade-adapter)
  2. [Inicializando aplicaciones hibridas Angular 1+2](#inicializando-aplicaciones-hibridas-angular-1-2)
  3. [Usando componentes Angular 2 en el codigo de Angular 1](#usando-componentes-angular-2-en-el-codigo-de-angular-1)
  4. [Usando `Component Directives` de Angular 1 en el codigo Angular 2](#usando-component-directives-de-angular-1-en-el-cdigo-angular-2)
  5. [Projectando contenido Angular 1 en componentes Angular 2](#projectando-contenido-angular-1-en-componentes-angular-2)
  6. [Transcluding contenido de Angular 2 en `Component Directives` de Angular 1](#transcluding-contenido-de-angular-2-en-component-directives-de-angular-1)
  7. [Hacer dependencias de Angular 1 injectables en Angular 2](#hacer-dependencias-de-angular-1-injectables-en-angular-2)
  8. [Hacer dependencias de Angular 2 injectables en Angular 1](#hacer-dependencias-de-angular-2-injectables-en-angular-1)
3. [Tutorial de actualizacion PhoneCat](#tutorial-de-actualizacion-phonecat)
  1. [Migrar a Typescript](#migrar-a-typescript)
  2. [Instalar Angular 2](#instalar-angular-2)
  3. [Inicilizacion hibrida 1+2 PhoneCat](#inicializcion-hibrida-1-2-phonecat)
  4. [Actualizar el servicio Phone](#actualiar-el-servicio-phone)
  5. [Actualizar componentes](#actualizar-componentes)
  6. [Migrar al router Angular 2 e inicilizar](#migrar-el-router-angular-2-e-inicializar)
  7. [Despidiendo a Angular 1](#despidiendo-a-angular-1)
4. [Apendice: Actualizando tests de PhonceCat](#apendice-actualizando-tests-de-phonecat)


## Preparacion

Hay muchas maneras de estructurar una aplicacion Angular 1. Cuando empezamos a
migrar a Angular 2 algunas formas de organzacion presentaran ventajas para el 
trabajo a realizar. Estas son algunas tecnicas y patrones que podemos aplicar 
mismo antes de iniciar la migracion.

## Usando la guia de estilos de Angular

La Guia de Estilos de Angular 1 recopila patrones y practicas para disponer de 
aplicaciones mantenibles en Angular 1. Contiene informacion de como escribir y 
organizar en codigo Angular, e igual de importante, que no se debe hacer al 
escribir y organizar la aplicacion.

Angular 2 es una evolucion de Angular 1, esta evolucion ha sido tomando las 
mejores partes de Angular 1. Angular 2 es mucho mas que esto por supuesto, pero
debemos tener en cuenta que siguiendo esta guia de estilos en una aplicacion
Angular 1 estaremos en alineacion y mejor estado hacia una futura version de 
nuestra aplicacion usando Angular 2.


Hay un grupo especial de reglas que hacen mucho mas sencillo una migracion
incremental hacia Angular 2 usando el modulo `upgrade`:

* La regla 1 define que se debe tener un archivo por componente. Esto nos 
permite que podemos encontrar facilmente un componente y que sea mas sencillo
navegar nuestro codigo. Y tambien permite que podamos migrar de Angular 1 a 
Angular 2 de uno en uno estos componentes. En este ejemplo cada controlador, 
cada servicio, componente y/o filtros esta definido en su propio archivo.
* La regla de estructura de "Directorio por feature" y Modularidad define el 
mismo concepto en un nivel mayor de abstraccion. Diferentes partes de la 
aplicacion deben tener sus correspondientes directorios y modulos Angular.

Cuando una aplicacion tiene esta estructura y organizacion, es posible realizar
una migracion de a una feature por vez. Si una aplicacion no tiene esta 
organizacion, aplicar la guia de estilos de Angular es el mejor punto de 
partida. Esta es una buena recomendacion no solo para aplicaciones que se desee
migrar.

## Usando un `Module Loader`

Al organizar la aplicacion en un archivo por componente, generalmente 
terminamos con un numero de muchos archivos pequeños. Esto es mucho mejor que
tener pocos archivos de gran tamaño, pero no es muy eficiente cargar todos 
estos archivos en un html en los tags `script`. Especialmente cuando tenemos 
que mantener el order en que son cargados. Esta es una importante razon para 
usar un `module loader`.

Usando un `module loader` como SystemJS, Webpack o Browserify nos permite hacer
uso del sistem de modulos incluidos en Typescript or ES2015 en nuestras 
aplicaciones. Podemos usar `import` y `module.export`. En ambos casos, el 
`module loader` se encarga de cargar cualquier codigo necesario y en el orden
correspondiente.

Cuando instalamos nuestra aplicacion en produccion, el `module loader` se 
encargara del empaquetado correspondiente.

## Migrando a Typescript

Si parte de nuestro plan de actualizacion a Angular 2 es utilizar Typescript, 
es recomendable incorporar lo necesario para la compilacion antes de empezar
la migracion. Teniendo de esta manera una cosa menos que aprender y en la cual
pensar durante la migracion. Tambien nos habilita a usar Typescript en nuestro
codigo Angular 1.

Dado que Typescript es un superset de ES2015, que es un superset de ECMAScript 
5, "cambiar" a Typescript no requiere mas que instalar el compilador y 
renombrar los archivos de `*.js` a `*.ts`. Podemos hacer algunos pasos 
adicionales para otener un mayor beneficio:
* Para aplicaciones que usen un `module loader` Typescript imports y exports 
(que son en realidad ECMAScript 2015 imports y exports) se pueden ayudar para
organizar el codigo en modulos. 
* Se pueden agregar los type a las funciones y variables de forma gradual para
sumar el beneficio de checkeo de errores en el momento de compilacion, 
autocomplete y documentacion.
* Tambien se puede agregar de forma gradual las nuevas funcionalidades de 
ES2015, como let, const, parametros default para funciones, etc.
* Controllers y servicios pueden ser convertidos a clases. De esta manera se
el codigo será mas cercano a la forma de Angular 2, siendo mas facil el proceso
de migracion.

## Usando `Component Directives`

En Angular 2, los componentes son la unidad basica de la cual se construyen las
 interfases de usuario. Las diferentes partes de una UI se definen a travez de 
 componentes, para construir las vistas del sistema.

Esto tambien es posible en Angular 1, usando `component directives`. Estas son 
directivas que contiene un controlador, la vista, inputs/outputs bindings; esta 
estructura es compartida en Angular 2. Las aplicaciones construidas usando 
`component directives` son mucho mas faciles de migrar a Angular 2 que otras 
aplicaciones que hagan uso de `ng-controller` u `ng-include` y/o herencia de 
`scope`.

Para que un `component directive` sea compatible en Angular 2 se debe aplicar 
la siguiente configuracion:

* `restrict: 'e'`. Los componentes se usan usualmente como elementos.
* `scope: {}` - un scope aislado. En Angular 2 los componentes siempre estan 
aislados de su contexto, y debemos aplicar la misma regla en Angular 1.
* `bindingToController: {}`. Los inputs y outputs de un componente deben ser 
asociados al controlador y no al `$scope`.
* `controller` y `controllerAs`. Un componente tiene su propio controlador.
* `template` o `templateUrl`. Un componente tiene su propia vista.

Los `component directives` pueden tambien usar los siguientes atributos:

* `transclude: true`, si el componente necesita hacer transclude de 
contenidos de algun origen.
* `require`, si el componente necesita comunicarse con el controlador de un 
componente padre.

Un `component directive` *no* debe usar los siguientes atributos:
* `compile`. Esto no estara soportado en Angular 2.
* `replace: true`. En Angular 2 nunca se reemplaza el componente por su 
template. Esto ya esta deprecado en Angular 1.
* `priority` y `terminal`. Aunque pueden ser usados en Angular 1, esto no es 
posible en Angular 2; por lo tanto, no es recomendable hacer uso de estos 
atributos.

El siguiente codigo presenta un ejemplo en Angular 1 de un `component 
directive` que cumple con el formato esperado para Angular 2:

```
export function heroDetailDirective() {
  return {
    restrict: 'E',
    scope: {},
    bindToController: {
      hero: '=',
      deleted: '&'
    },
    template: `
      <h2>{{ctrl.hero.name}} details!</h2>
      <div><label>id: </label>{{ctrl.hero.id}}</div>
      <button ng-click="ctrl.onDelete()">Delete</button>
    `,
    controller: function() {
      this.onDelete = () => {
        this.deleted({hero: this.hero});
      };
    },
    controllerAs: 'ctrl'
  };
}
```

Angular 1.5 incorporó una [API para componentes](https://docs.angularjs.org/api/ng/type/angular.Module) 
que facilita la cracion de este tipo de directivas. Se presentan los siguientes
 beneficios del uso de esta API:
 * Require menos codigo repetitivo.
 * Refuerza el uso de buenas practicas como `controllerAs`.
 * Tiene valores por default para attributos de la directiva como `scope` y 
 `restrict`.

Usando la API de componentes la directiva anterior resultaria en:
```
export const heroDetail = {
  bindings: {
    hero: '<',
    deleted: '&'
  },
  template: `
    <h2>{{$ctrl.hero.name}} details!</h2>
    <div><label>id: </label>{{$ctrl.hero.id}}</div>
    <button ng-click="$ctrl.onDelete()">Delete</button>
  `,
  controller: function() {
    this.onDelete = () => {
      this.deleted(this.hero);
    };
  }
};
```

Angular 1.5 incorpora metodos al ciclo de vida del controllador de esta 
directiva de componentes `$onInit()`, `$onDestroy()` y `$onChange()`. En 
Angular 2 comparte esta arquitectura en el ciclo de vida del componente,
presentando de esta manera una forma conveniente para su compatibilidad
entre versiones.

## Actualizando con el `Upgrade Adapter`

El module `upgrade` de Angular 2 es una herramienta muy util para realizar
la actualizacion de una aplicacion. Con esta herramienta podemos disponer de 
componentes Angular 1 y Angular 2 en una misma aplicacion y hacerlos funcionar
sin problemas. Esto nos permite hacer una actualizacion por partes dado que 
se podra tener una coexistencia de los dos frameworks durante el periodo de 
transicion. 

## Como funciona el `Upgrade Adapter`

La herramienta primaria del modulo de actualizacion se llama `UpgradeAdapter`. 
Este es un servicio hibrido que puede manejar codigo de Angular 2 y Angular 1.

Cuando usamos `UpgradeAdapter` en realidad estamos ejecutando las dos versiones
de Angular al mismo tiempo. Todo el codigo de Angular 2 se ejecuta en el lado
de Angular 2 y el codig de Angular 1 en su correspondiente parte. Ambas 
versiones disponen de una version completa de la version de Angular donde se 
esta ejecutando. No hay ningun emulador ejecutandose, con lo que el 
comportamiento esperando es el normal de una aplicacion en cada uno de los 
frameworks.

Lo que sucede en en un nivel general es que los componentes y servicios 
manejados por cada framework pueden comunicarse con los componentes y servicios
del otro framework. Esto sucede en tres areas principales: Injeccion de 
depdencias, el DOM, y la deteccion de cambios.

#### Deteccion de cambios
La injeccion de dependencias es central en Angular 1 y Angular 2, pero existen
diferencias significativas en como funcionan en cada version.

Angular 1                              Angular 2
Los token de ID son siempre strings    Los [tokens pueden ser de diferentes 
                                       tipos](https://angular.io/docs/ts/latest/guide/dependency-injection.html). Generalmente son clases y tambien
                                       pueden ser strings.
Hay un unico injector. Inclusive en    Hay un [arbol de herencia de injectores](https://angular.io/docs/ts/latest/guide/hierarchical-dependency-injection.html),
aplicaciones multi-modulos, todo se    con un injector raiz para cada 
incorpora a un solo namespace.         componente.

A pesar de estas diferencias, podemos tener interoperabilidad de la inyeccion
de dependencias. El `UpgradeAdapter` resuelve las diferencias y se ocupa de 
que todo trabaje sin problemas:
* Podemos hacer disponibles servicios escritos en Angular 1 para inyeccion en
Angular 2 al *actualizarlos*. La misma instancia singleton de cada servicio es
compartida en los dos frameworks. En Angular 2 estos servicios estaran siempre
en el inyector raiz y disponible para todos los componentes. Siempre tendran
tokens strings, los mismos tokens que tendrian en Angular 1.

* Tambien podemos hacer disponibles los servicios en Angular 2 para ser 
injectados en Angular 1 mediante la una degradacion. Solo los servicios que
esten definidos en el injector raiz de Angular 2 pueden ser degradados. De 
nuevo, la misma instancia del servicio Singleton es compartida a los dos 
frameworks. Se define un token string de forma explicita que puede ser usado 
en Angular 1.

![alt text](https://angular.io/resources/images/devguide/upgrade/injectors.png "The two injectors in a hybrid application")

#### Componentes y el DOM

En el DOM de una aplicacion hibrida encontraremos componentes y directivas 
tanto de Angular 1 como de Angular 2. Estos componentes se comunican entre
si haciendo uso de los entradas y salidas de los bindings de cada framework
respectivamente, por medio del puente provisto por  el `UpgradeAdapter`. 
Tambien pueden comunicarse por medio de la injeccion de dependencia descripta
anteriormente.

Hay dos puntos principales para entender que es lo que sucede en el DOM de una
aplicacion hibrida:
* Cada elemento en el DOM pertenece solamente a uno de los dos frameworks. El 
otro framework no conoce de él. Si el dueño de un elemento es Angular 1, 
Angular 2 lo trata como si no existier, y viceversa.
* La raiz de una aplicacion *es siempre un template de Angular 1*.

Por lo tanto una aplicacion hibrida comienza como una aplicacion Angular 1, y
es Angular 1 es quien procesa el raiz de su template. Ese componente puede 
entonces ser manejado por Angular 2, y puede hacer uso de cualquier numero de 
componentes y directivas de Angular 2.

Mas allá de esta caracteristica inicial, podemos hacer uso de cualquier 
combinacion de Angular 1 o Angular 2 elementos. Siempre vamos a cruzar los 
limites de cualquier de los dos frameworks en una de las dos siguientes 
formas:

- Usando un componente del otro framework: Una template de Angular 1 usando
un componente Angular 2, una template Angular 2 usando un componente Angular 1.
- Por medio de transcluding o projectando contenido del otro framework. El 
`UpgradeAdapter` vincula los conceptos relacionados de la transclusión en 
Angular 1 en la proyección de contenido Angular 2 juntos.

![alt text](https://angular.io/resources/images/devguide/upgrade/dom.png "DOM element ownership in a hybrid application")

En calquier parte que hagamos uso de un componente que pertenezca al otro 
framework, consideremos que usamos un componente de Angular 2 desde Angular 1
de la siguiente manera:

```
  <ng2-component></ng2-component>
```

#### Deteccion de cambios
La deteccion de cambios en Angular 1 se trata unicamente de `scope.$apply()`. 
Despues de que ocurre un evento, se ejecuta `scope.$apply()`. Esto puede ser
ejecutado de forma automatica por el framework, o en algunos casos de forma 
manual en nuestro codigo. Este es el proceso que actualiza el bindings de los
datos.

En Angular 2 las cosas son diferentes. Mientras que la deteccion de cambio 
ocurre despues de cada evento, ninguno tiene que llamar a `scope.$apply()`
para que suceda. Esto se debe a que todo en Angular 2 es ejecutado dentro de 
algo llamado [Angular Zone](https://angular.io/docs/ts/latest/api/core/index/NgZone-class.html).
Angular siempre conoce cuadno el codigo finaliza, por lo tanto conoce cuando 
debe ejecutar la deteccion de cambios. El codigo no debe llamar a 
`scope.apply()` o nada parecido por si mismo.

En el caso de aplicaciones hibridas, el `UpgradeAdapter` hace de puente entre 
las estrategias de Angular 1 y Angular 2. Esto es lo que sucede:

* Todo lo que sucede en la aplicacion se ejecuta dentro de Angular 2 zone. Esto
es valido tanto para un evento originado en un codigo Angular 1 o Angular 2. La
zone dispara Angular 2 deteccion de cambio despues de cada evneto.
* El `UpgradeAdapter` invocara `$rootScope.$apply()` de Angular 1 despues de 
cada ejecucion de Angular zone. Esto tambien dispara la deteccion de cambio de 
Angular 1 despues de cada evento.

![alt text](https://angular.io/resources/images/devguide/upgrade/change_detection.png "Change detection in a hybrid application")

En la practica, esto significa que no tenemos la necesidad de llamar a 
`$apply()` en nuestro codigo, no importa si es desde Angular 1 o Angular 2. El
`UpgradeAdapter` realiza este trabajo por nosotros. Sin embargo podemos llamar 
a `$apply()` desde nuestro codigo, haciendo que no sea necesario que eliminemos
cualquier llamada que tengamos en nuestro codigo. Solo debemos tener presente
que estas llamadas no tendran ningun efecto en una aplicacion hibrida.

Cuando degradamos un componente de Angular 2 y lo usamos en Angular 1, los 
inputs del componente se los inspecccionara usando la dereccion de cambios de 
Angular 1. Cuando esos inputs cambian, las propiedades que correspondan del 
componente se setean. Tambien podemos intervenir en los cambios implementando
la interfase [OnChanges](https://angular.io/docs/ts/latest/api/core/index/OnChanges-class.html) en el componente, de la misma manera que si no fuera
degradado.

De la misma manera, cuando actualizamos un componente Angular 1 y lo usamos en 
Angular 2, todos los bindings definidos por la directiva componente en el `scope`
(o `bindingToController`) sera enlazado a la deteccion de cambios de Angular 2. 
Serán tratados como inputs normales de Angular 2 y seteados al scope (o 
controlador) cuando cambian.

#### Usando el `UpgradeAdapter` con Angular 2 `NgModules`

Angular 1 y Angular 2 tiene su concepto propio de modules para ayudar a 
organizar la aplicacion en blockes cohesivos de funcionalidad.

Los detalles de cada uno difieren en arquitectura e implementacion. En Angular
1, agregamos assets a `angular.module`. En Angular 2, cramos clases a las que
se le aplican el decodador `NgModule` que describen el metadata de Angular. Las
diferencias partes desde ahi.

En una aplicacion hibrida  se ejecutan las dos versiones de Angular al misom 
tiempo. Esto significa que necesitamos al menos un moduleo de cada version. 
Usaremos el modulo de Angular 2 en el `UpgradeAdapter` mientras que el modulo 
de Angular 1 para inicilizar. Veamos como.

Mas informacion acerca de Angular 2 modules en [NgModule guide](https://angular.io/docs/ts/latest/guide/ngmodule.html)

## Inicializando aplicaciones hibridas Angular 1+2

El primer paso para actualizar una aplicacion usando el `UpgradeAdapter` es 
inicializarla como una aplicacion hibrida que soporte Angular 1 y Angular 2.

Una aplicacion Angular 1 pura puede inicializarse en una de dos maneras: 
Usando la directiva `ng-app` the alguna parte del html, o llamando a 
`angular.bootstrap` desde JavaScript. En Angular 2, solo la segunda forma es
posible. Este es tambien le caso de una aplicacion hibrida. Por lo tanto es un
buen paso transformar la aplicacion Angular 1 para usar JavaScript bootstrap 
mismo antesde cambiar a que sea una aplicacion hibrida.

Supogamos que tenemos una aplicacion que utiliza `ng-app` de la siguiente 
manera: 

```
<!DOCTYPE HTML>
<html>
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.3/angular.js"></script>
    <script src="app/1-ng-app/app.module.js"></script>
  </head>
  <body ng-app="heroApp" ng-strict-di>
    <div id="message" ng-controller="MainCtrl as mainCtrl">
      {{ mainCtrl.message }}
    </div>
  </body>
</html>
``` 

Podemos remover las directivas `ng-app` y `ng-strict-di` del html, y en lugar 
de llamar `angular.bootstrap` desde Javascript, que terimara en algo de este 
estilo:

```
angular.bootstrap(document.body, ['heroApp', {strictDi: true}]);
```

Ahora podemos introducir Angular 2 al proyecto. Inspirado en las instrucciones 
provistas por [QuickStart](https://angular.io/docs/ts/latest/quickstart.html), se
puede copiar selectivamente usando el repositorio [repositorio QuickStart github](https://github.com/angular/quickstart).

El paso siguiente es crear el archivo `app.module.ts` y la clase `NgModule`:

```
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
@NgModule({
  imports: [ BrowserModule ]
})
export class AppModule {}
```

Este minimo uso de `NgModule` importa `BrowserModule`, el module que toda app 
Angular basada en el uso de un browser. Ese metodo toma los mismos argumentos 
que `angular.bootstrap`:

```
import { UpgradeAdapter } from '@angular/upgrade';
/* . . . */
const upgradeAdapter = new UpgradeAdapter(AppModule);
upgradeAdapter.bootstrap(document.body, ['heroApp'], {strictDi: true});
```

Felicitaciones, ya estamos ejecutando una aplicacion hibrida Angular 1+2!
El codigo existente en Angular 1 funciona igual que antes y esta lista para
usar Angular 2.

```
Notese que, distinto de `angular.bootstrap`, el `upgradeAdapter.bootstrap` se 
ejecuta de forma *asincronica*. La aplicacion no es ejecutada de forma 
inmediata. Un tiempo debe pasar despues de que la inicialiazion se llamó. 
``` 

Cuando empezaron a migrar componentes a Angular 2, usaremos el `UpgradeAdapter`
para otras tareas adicionales que solo la inicializacion. Es importante usar la
misma instancia del adaptador en toda la aplicacion, dado que guarda 
informacion acerca de que esta ocurriendo en la apliacion. Será util tener un
modulo para compartir la instancia de `UpgradeAdapter` en el proyecto:

``` upgrade_adapter.ts
import { UpgradeAdapter } from '@angular/upgrade';
export const upgradeAdapter = new UpgradeAdapter(AppModule);
``` 

Esta instancia compartida puede ser obtenida en un modulo cuando la 
necesitamos:

```
import { upgradeAdapter } from './upgrade_adapter';
/* . . . */
upgradeAdapter.bootstrap(document.body, ['heroApp'], {strictDi: true});
```

## Usando componentes Angular 2 en el codigo de Angular 1

[!alt text](https://angular.io/resources/images/devguide/upgrade/a1-to-a2.png "Using an Angular 2 component from Angular 1 code")
Una vez que estamos ejecutando nuestra aplicacion hibrida, podemos empezar el 
proceso gradual de actualizacion. Esto puede ser un componente completamente 
nuevo o un componente que fuera parte de Angular 1 anteriorment y que se 
reescriba en Angular 2.

Digamos que tenemos un componente en Angular 2 que muestra informacion de un 
Hero:

```
import { Component } from '@angular/core';
@Component({
  selector: 'hero-detail',
  template: `
    <h2>Windstorm details!</h2>
    <div><label>id: </label>1</div>
  `
})
export class HeroDetailComponent {
}
```

Si queremos usar este componente desde Angular 1, necesitamos degradarlo 
utilizando el upgrade adapter. Lo que obtenemos cuando hacemos esto es una 
directiva Angular 1, que podemos registrar en nuestro modulo Angular 1:

```
import { HeroDetailComponent } from './hero-detail.component';
/* . . . */
angular.module('heroApp', [])
  .directive('heroDetail', upgradeAdapter.downgradeNg2Component(HeroDetailComponent));
```

Dado que `HeroDetailComponent` es un componente Angular 2, necesitamos 
agregarlo en las `declarations` en `AppModule`.

```
import { HeroDetailComponent } from './hero-detail.component';
@NgModule({
  imports: [ BrowserModule ],
  declarations: [ HeroDetailComponent ]
})
export class AppModule {}
```

Todos los componentes, directivas y pipes en Angular 2 deben ser incluidos en 
`NgModule`.

Obtendremos como resultado una directiva Angular 1 llamada `heroDetail`, que
podemos usar de la misma forma que cualquier otra directiva en los templates
de Angular 1.

```
<hero-detail></hero-detail>
```

Notese que esta directiva en Angular 1 es una directiva de elemento 
(`restrict: 'E'`) llamada `heroDetail`. Una directiva de elemento en Angular 1
se use con un elemento que sea equivalente a su nombre. *El metadata `selector`
del degradado del componente de Angular 2 es ignorado*.

Muchos componente no son tan simples como este, por supuesto. Muchos de ellos
tienen *inputs* y *outpus* que los conectan con el mundo exterior. Un 
componente hedo detail en Angular 2 con inputs y outputs luce de la siguiente
manera:

```
import { Component, EventEmitter, Input, Output } from '@angular/core';
import { Hero } from '../hero';
@Component({
  selector: 'hero-detail',
  template: `
    <h2>{{hero.name}} details!</h2>
    <div><label>id: </label>{{hero.id}}</div>
    <button (click)="onDelete()">Delete</button>
  `
})
export class HeroDetailComponent {
  @Input() hero: Hero;
  @Output() deleted = new EventEmitter<Hero>();
  onDelete() {
    this.deleted.emit(this.hero);
  }
}
```

Estos inputs y outputs pueden ser provistos desde un template Angular 1, y el 
`UpgradeAdapter` realiza el trabajo de hacer los bindings correspondientes:

```
<div ng-controller="MainController as mainCtrl">
  <hero-detail [hero]="mainCtrl.hero"
               (deleted)="mainCtrl.onDelete($event)">
  </hero-detail>
</div>
```

Notese de que a pensar de que estamos en una template de Angular 1, *estamos
usando la sintaxis de atributos de Angular 2 para los bindings de inputs y 
ouputs*. Esto es un requerimiento para los componentes degradados. Las 
expresiones utilizadas son expresiones de la forma de Angular 1.

*Se debe usar KEBAB-CASE para los atributos de un componente degradado*

Hay una excepcion especial para usar la regla de la sintaxis de los atributos 
de un componente Angular 2. Es aplicable a los nombres de los inputs y outputs
que consisten en multiples palabras. En Angular 2 usamos camelCase:

`[myHero]="hero"`

pero cuando lo usamos desde un template de Angular 1, necesitmos usar 
kebab-case:

`[my-hero]="hero"`


La variables `$event` puede ser usada en los outputs para acceder al objeto que
fue emitido. En este caso será el objeto Hero, dado que eso fue enviado a 
`this.deleted.emit()`.

Dado que esto es un template de Angular 1, podemos seguir usando directivas
Angular 1 en el elemento, mismo si tiene atributos bideados en el. Podemos por
ejemplo hacer copias del elemento usando `ng-repeat`:

```
<div ng-controller="MainController as mainCtrl">
  <hero-detail [hero]="hero"
               (deleted)="mainCtrl.onDelete($event)"
               ng-repeat="hero in mainCtrl.heroes">
  </hero-detail>
</div>
```

## Usando `Component Directives` de Angular 1 en el codigo Angular 2

[!alt text](https://angular.io/resources/images/devguide/upgrade/a2-to-a1.png "Using an Angular 1 component from Angular 2 code")

Entonces, podemos crear un componente en Angular 2 y usarlo desde Angular 1. 
Esto es muy util cuando comenzamos nuestra migracion para componentes de bajo 
nivel y luego vamos migrando el resto. Pero en algunos casos es conveniente
hacer las cosas de forma inversa: comenzar por componentes de alto nivel y 
luego ir hacia abajo. Esto tambien puede hacerse usando el `UpgradeAdapter`.
Podemos *actualizar* directivas de Angular 1 y usarlas en Angular 2.

No todo tipo de directivas Angular 1 pueden ser actualizadas. El tipo de 
directiva actualizable es la de tipo componente, con las caracteristicas 
descriptas anteriormente. La mejor forma de asegurar compatibilidad es utilidad
la API de componentes de Angular 1.5.

Un ejemplo simple de la actualizacion de un componente es uno con unicamente un 
controlador y una template:

```
export const heroDetail = {
  template: `
    <h2>Windstorm details!</h2>
    <div><label>id: </label>1</div>
  `,
  controller: function() {
  }
};
```

Podemos *actualizar* este componente a Angular 2 usando el metodo 
`upgradeNg1Component` de `UpgradeAdapter`. Este metodo necesita el nombre del 
componente Angular 1 y devuelve una **clase componente** en Angular 2. Debemos
inlcuir esta clase en `NgModule` como cualquier otro componente Angular 2:

```
const HeroDetail = upgradeAdapter.upgradeNg1Component('heroDetail');
@NgModule({
  imports: [ BrowserModule ],
  declarations: [ ContainerComponent, HeroDetail ]
})
export class AppModule {}
```

> Un componente actualizado siempre tiene un elemento selector, que es basado
en el nombre original de la directiva componente en Angular 1.

                      Definicion de Binding   Sintaxis del template
Binding de Atributo   myAttribute:            <my-component myAttribute="value">
                      '@myAttribute'
Binding de Expresion  myOutput:               <my-component (myOutput)="action()">
                      '&myOutput'
Binding de una via    myValue:                <my-component [myValue]="anExpression">
                      '<myValue'
Binding de dos vias   myValue:                Binding de dos vias: <my-component [(myValue)]="anExpression">
                      '=myValue'              En la mayoria de los casos binding de una via es suficiente,
                                              [myValue]="anExpression">.

Como ejemplo, supongamos que tenemos una directiva-componente en Angular 1
que sea el detalle de un Hero:

```
export const heroDetail = {
  bindings: {
    hero: '<',
    deleted: '&'
  },
  template: `
    <h2>{{$ctrl.hero.name}} details!</h2>
    <div><label>id: </label>{{$ctrl.hero.id}}</div>
    <button ng-click="$ctrl.onDelete()">Delete</button>
  `,
  controller: function() {
    this.onDelete = () => {
      this.deleted(this.hero);
    };
  }
};
```

Podemos actualizar este componente a Angular 2, y proveer el input y el output
usando la sintaxis de template de Angular 2:

```
import { Component } from '@angular/core';
import { Hero } from '../hero';
@Component({
  selector: 'my-container',
  template: `
    <h1>Tour of Heroes</h1>
    <hero-detail [hero]="hero"
                 (deleted)="heroDeleted($event)">
    </hero-detail>
  `
})
export class ContainerComponent {
  hero = new Hero(1, 'Windstorm');
  heroDeleted(hero: Hero) {
    hero.name = 'Ex-' + hero.name;
  }
}
```

## Projectando contenido Angular 1 en componentes Angular 2

[!alt text](https://angular.io/resources/images/devguide/upgrade/a1-to-a2-with-projection.png "Projecting Angular 1 content into Angular 2")
Cuando usamos un componente degradado de Angular 2 desde una template Angular 1
, puede presentarse la necesidad de hacer transclude en él. Esto tambien es 
posible. Debido a que no existe nada con el concepto de transclude en Angular 2
, tenemos un concepto similar llamado `proyecccion de contenido`. El 
`UpgradeAdapter` permite que las dos funcionalidades puedan interoperar entre 
si.

Un componente de Angular 2 que soporta `proyeccion de contenido` utiliza el tag
 `<ng-content>`. Ejemplo:

```
import { Component, Input } from '@angular/core';
import { Hero } from '../hero';
@Component({
  selector: 'hero-detail',
  template: `
    <h2>{{hero.name}}</h2>
    <div>
      <ng-content></ng-content>
    </div>
  `
})
export class HeroDetailComponent {
  @Input() hero: Hero;
}
```

Cuando usamos este componente desde Angular 1, podemos proveer de contendidos 
hacia el. De la misma manera que puede usarse el transclude en Angular 1, se
include el contenido en el tag <ng-content> de Angular 2.

```
<div ng-controller="MainController as mainCtrl">
  <hero-detail [hero]="mainCtrl.hero">
    <!-- Everything here will get projected -->
    <p>{{mainCtrl.hero.description}}</p>
  </hero-detail>
</div>
```

> Cuando el contenido de Angular 1 es proyectado dentro de un componente de
Angular 2, todavia se encuentra en "tierra de Angular 1" y es manejado por
Angular 1.

## Transcluding contenido de Angular 2 en `Component Directives` de Angular 1

[!img text](https://angular.io/resources/images/devguide/upgrade/a2-to-a1-with-transclusion.png "Projecting Angular 2 content into Angular 1")
De la misma forma que podemos proyectar contenido de Angular 1 dentro de 
componentes de Angular 2, podemos hacer transclude contenido de de Angular 2
dentro de Angular 1, siempre que estemos haciendo uso de versiones actualizadas
 de estos.

Cuando una directiva de componente soporta transcluding, puede utilizar el tag
de directive `ng-transclude` para marcar el punto de transcluding.

```
export const heroDetailComponent = {
  bindings: {
    hero: '='
  },
  template: `
    <h2>{{$ctrl.hero.name}}</h2>
    <div>
      <ng-transclude></ng-transclude>
    </div>
  `
};
```

> Esta directiva necesita tener `transclude: true` habilitado. Esta habilitado
por default en la API de directiva componente en Angular 1.5.

Al actualizar este componente y usandolo desde Angular 2, podemos popular el 
tag del componente con contenidos que sera usado con transluding:

```
import { Component } from '@angular/core';
import { Hero } from '../hero';
@Component({
  selector: 'my-container',
  template: `
    <hero-detail [hero]="hero">
      <!-- Everything here will get transcluded -->
      <p>{{hero.description}}</p>
    </hero-detail>
  `
})
export class ContainerComponent {
  hero = new Hero(1, 'Windstorm', 'Specific powers of controlling winds');
}
```

## Hacer dependencias de Angular 1 injectables en Angular 2

Cuando ejecutamos una aplicacion hibrida, se presentan casos donde debemos 
poder hacer uso de las dependencias de Angular 1 injectable en el codigo de
Angular 2. Eso puede ser porque tenemos parte de nuestra logica en servicios de
 Angular 1, o necesitamos algun servicio que solo existe en Angular 1 como
`$location` o `$timeout`.

En estas situaciones, es posible *actualizar* un provider de Angular 1 a 
Angular 2. Esto habilita injectarlos en alguna parte de Angular 2. Por ejemplo,
 podemos tener un servicio HeroesService en Angular 1:

```
import { Hero } from '../hero';
export class HeroesService {
  get() {
    return [
      new Hero(1, 'Windstorm'),
      new Hero(2, 'Spiderman')
    ];
  }
}
```

Podemos actualizar el servicio usando `upgradeNg1Provider` de `UpgradeAdapter`
dando el nombre del servicio. Esto agrega el servicio en el raiz del injector 
de Angular 2.

```
angular.module('heroApp', [])
  .service('heroes', HeroesService)
  .directive('heroDetail',
    upgradeAdapter.downgradeNg2Component(HeroDetailComponent));
upgradeAdapter.upgradeNg1Provider('heroes');
```

Con esta adaptacion podemos injectarlo en Angular 2 usando el token string que
equivale al nombre original en Angular 1:

```
import { Component, Inject } from '@angular/core';
import { HeroesService } from './heroes.service';
import { Hero } from '../hero';
@Component({
  selector: 'hero-detail',
  template: `
    <h2>{{hero.id}}: {{hero.name}}</h2>
  `
})
export class HeroDetailComponent {
  hero: Hero;
  constructor(@Inject('heroes') heroes: HeroesService) {
    this.hero = heroes.get()[0];
  }
}
```

> En este caso hemos actualizado un clase servicio, que tiene el beneficio de 
user la anotacion TypeScript cuando lo injectamos. Mientras que esto no afecta
como es manejada la injeccion, habilita tambien el beneficio de chequeo 
estatico de tipos. A pesar de que no es requerido, todo servicio, factoria o
proveedor en Angular 1 es posible ser actualizado.

## Hacer dependencias de Angular 2 injectables en Angular 1

De forma adicional a la actualizacion de las dependencias de Angular 1, podemos
degradar una dependencia Angular 2, para poder usarla en Angular 1. Esto es 
util cuando empezamos a migrar servicios a Angular 2 o creando nuevos servicios 
en Angular 2 mientras que seguimos teniendo componentes escritos en Angular 1.

Por ejemplo, tenemos un servicio en Angular 2 llamado `Heroes`:

```
import { Injectable } from '@angular/core';
import { Hero } from '../hero';
@Injectable()
export class Heroes {
  get() {
    return [
      new Hero(1, 'Windstorm'),
      new Hero(2, 'Spiderman')
    ];
  }
}
```

Nuevamente, como otros componentes en Angular 2, registramos este proveedor en
`NgModule` en la lista de `providers`.

```
import { Heroes } from './heroes';
@NgModule({
  imports: [ BrowserModule ],
  providers: [ Heroes ]
})
export class AppModule {}
```

Ahora podemos enmascarar `Heroes` de Angular 2 en una function de factoria en 
Angular 1 usnado `upgradeAdapter.downgradeNg2Provider()` y conectar la factoria
en un modulo de Angular 1. El nombre de la dependencia en Angular 1 depende de 
uno:

```
angular.module('heroApp', [])
  .factory('heroes', upgradeAdapter.downgradeNg2Provider(Heroes))
  .component('heroDetail', heroDetailComponent);
```

Despues de esto, el servico es inyetable en cualquier parte de nuestro codigo
de Angular 1:

```
export const heroDetailComponent = {
  template: `
    <h2>{{$ctrl.hero.id}}: {{$ctrl.hero.name}}</h2>
  `,
  controller: ['heroes', function(heroes: Heroes) {
    this.hero = heroes.get()[0];
  }]
};
```

## Tutorial de actualizacion PhoneCat

En esta seccion veremos ua ejemplo completo de la actualizacion de una 
aplicacion usando el modulo `upgrade`. La aplicacion con la que vamos a 
trabajar es [Angular PhoneCat](https://github.com/angular/angular-phonecat) del [tutorial original de Angular 1](https://docs.angularjs.org/tutorial, 
donde muchos usuarios iniciaron sus aventuras en angular. Vamosa a ver como
migramos esta aplicacion a Angular 2.

Durante el proceso iremos aprendiendo como aplica los pasos mas importantes
de la guia de preparacion en practica: Alinearemos la aplicacion con Angular 2
y comenzaremos a usar TypeScript.

Para seguir este tutorial, se debe clonar el repositorio de [angular-phonecat](https://github.com/angular/angular-phonecat) 
y aplicar los pasos a medida que se avanza.

Esta es la estructura inicial:

angular-phonecat
-- bower.json
-- karma.conf.js
-- package.json
-- app
-- -- core
-- -- heckmark
-- -- -- checkmark.filter.js
-- -- -- checkmark.filter.spec.js
-- -- phone
-- -- -- phone.module.js
-- -- -- phone.service.js
-- -- -- phone.service.spec.js
-- -- core.module.js
-- -- phone-detail
-- -- -- phone-detail.component.js
-- -- -- phone-detail.component.spec.js
-- -- -- phone-detail.module.js
-- -- -- phone-detail.template.html
-- -- phone-list
-- -- -- phone-list.component.js
-- -- -- phone-list.component.spec.js
-- -- -- phone-list.module.js
-- -- -- phone-list.template.html
-- -- img
-- -- -- ...
-- -- phones
-- -- -- ...
-- -- app.animations.js
-- -- app.config.js
-- -- app.css
-- -- app.module.js
-- -- index.html
-- e2e-tests
-- -- protractor-conf.js
-- -- scenarios.js 

Este es un buen punto de inicio. El codigo usa la API de componentes de 
Angular 1.5 y la organizacion sigue la guia de estilos de Angular 1, el cual 
es un paso importante de la preparacion antes de una migracion satisfactoria.

* Cada componente, servicio y fltros este definido en su propio archivo, como
describe la regla 1.
* Los modulos `core`, `phone-detail` y `phone-list` estan cada uno en su propia
carpeta. Esos subdirectorios contienen el codigo JavaScript, los templates html
que pertenece a cada feature. Esto va e linea con las reglas de 
Folders-by-Feature Structure y Modularity.
* Los tests de unidad estan completos para cada feature y son sencillos de 
ubicar como describe la organizacion de los tests.

## Migrar a Typescript

Dado que vamos a usar TypeScript para desrrollar la aplicacion en Angular 2, 
el primer paso es intalar el compilador mismo antes de actualizar.

Iremos migrando los paquetes definidos con Bower en favor de NPM. Instalaremos
las nuevas dependencias usando NPM, brindando la posibilidad al final de 
remover Bower.

Comencemos instalando TypeScript en el poyecto:

`npm i typescript --save-dev`

Agreguemos un script de ejecucion para el compilador de TypeScript tsc a 
`package.json`:

```
{
  "scripts": {
    "tsc": "tsc",
    "tsc:w": "tsc -w"
  }
}
```

Ahora podemos instalar la definicion de tipos de las librerias existentes que
no estan incluidas en los tipos preempaquetados: Angular 1 y Jasmine Unit tests
framework.

```
npm install @types/jasmine
            @types/angular
            @types/angular-animate
            @types/angular-cookies
            @types/angular-mocks 
            @types/angular-resource 
            @types/angular-route 
            @types/angular-sanitize --save-dev
```

Tambien debemos configurar TypeScript para que tenga conocimiento de nuestro 
proyecto. Agregaremos un archivo tsconfig.json en el directorio del projecto, 
de la misma manera que lo hicimos en Quickstart. Esto instruye al compilador de 
TypeScript como interpretar nuestro codigo fuente.

```
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "moduleResolution": "node",
    "sourceMap": true,
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "removeComments": false,
    "noImplicitAny": false,
    "suppressImplicitAnyIndexErrors": true
  }
}
``` 

Le indicamos al compilador de TypeScript compilar los archivos a ES5 agrupados
en modulos CommonJS.

Podemos ahora inicializar el compilador de TypeScript desde la linea de 
comandos. Pondremos a observar los archivos .ts y compilarlos a JavaScript on 
the fly. Esos archivos compilados .js son cargados por el navegador por 
SystemJS. Este es un procesos que queremos que se ejecute en segundo plano de
forma automatica.

`npm run tsc:w`

El siguiente paso que se realiza será convertir nuestros archivos JavaScript en
TypeScript. Dado que TypeScript es un superset de ECMAScript 2015, que es un 
superset de ECMAScript 5, simplemente podes renombrar nuestros archivos de .js
a .ts y todo funcionara de forma habitual. Cada vez que el compilador de 
TypeScript se ejecute generará los archivos .js correspondientes que son los 
que son ejecutados. Si iniciamos el servidor http con `npm start`, deberiamos 
ver la aplicacion funcionando en el navegador.

Ahora que tenemos TypeScript funcionando, podemos empezar a beneficionarnos de
sus funcionalidades.

TypeScript es un superset de ES2015. Cualquier aplicacion que se escribio 
previamente en ES5 - como fue el caso de PhoneCat - puede empezar a incorporar
los beneficio de ES2015. Este include funcionalidades como `let` y `const`, 
arrow functions, parametros default, y asignaciones destructivas.

Otra cosa que podemos hacer es *type safety* a nuestro codigo. Esto ha sido 
parcialmente agregado de forma automatica cuando agregamos los tipos para 
Angular 1. TypeScript chequea de forma automatica que estemos llamando a la 
API de forma correcta cuando hacemos cosas como registrar componentes en los 
modulos de Angular.

Pero tambien podemos agregar *type annotations* para agregar un mayor beneficio
a nuestro sistema usando TypeScript. Por ejemplo podemos anotar el filtros
checkmark para verificar de forma explicita los argumentos booleanos. Esto 
presenta de forma mas clara la finalidad de filter.

```
angular.
  module('core').
  filter('checkmark', function() {
    return function(input: boolean) {
      return input ? '\u2713' : '\u2718';
    };
  });
```

En el servicio `Phone` podemos usar la anotacion explicita en la dependencia 
`$resource` con `angular.resource.IResourceService` - Un tipo definido en 
Angular 1 typings.

```
angular.
  module('core.phone').
  factory('Phone', ['$resource',
    function($resource: angular.resource.IResourceService) {
      return $resource('phones/:phoneId.json', {}, {
        query: {
          method: 'GET',
          params: {phoneId: 'phones'},
          isArray: true
        }
      });
    }
  ]);
```

Podemos aplicar el mismo trabajo en el archivo de configuracion de las rutas
`app.config.ts` donde usamos los servicios location y route. Agregando las 
anotaciones correspondientes en TypeScript podremos verificar si llamamos a
las API con los correspondientes argumentos.

```
angular.
  module('phonecatApp').
  config(['$locationProvider', '$routeProvider',
    function config($locationProvider: angular.ILocationProvider,
                    $routeProvider: angular.route.IRouteProvider) {
      $locationProvider.hashPrefix('!');
      $routeProvider.
        when('/phones', {
          template: '<phone-list></phone-list>'
        }).
        when('/phones/:phoneId', {
          template: '<phone-detail></phone-detail>'
        }).
        otherwise('/phones');
    }
  ]);
```
> *Angular 1 type definitions* que se utiliza no son oficialmente mantenidas
por el equipo de Angular, pero son muy completas. Es factible crear una 
aplicacion Angular 1 con soporte completo de tipos anotados haciendo uso de esta 
librearia.

> Si este es uno de nuestros objetivos, es una buena idea activar 
`noImplicityAny` como una opcion de configuracion en `tsconfig.ts`. Esto 
indicara que TypeScript muestre una advertencia para cualquier parte de 
nuestra aplicacion que no tenga las anotaciones de tipos incorporados. Esto
nos puede dar una guia acerca de la evolucion del soporte de tipos anotados
en nuestro proyecto.

Otra funcionalidad de TypeScript que podemos usar son las *clases*. Eso es el 
motivo por el cual estan las cases en ES2015/TypeScript, esto significa que 
podemos conectar nuestras clases como controladores de componentes y Angular 1
podrá usarlo.

```
class PhoneListController {
  phones: any[];
  orderProp: string;
  query: string;
  static $inject = ['Phone'];
  constructor(Phone: any) {
    this.phones = Phone.query();
    this.orderProp = 'age';
  }
}
angular.
  module('phoneList').
  component('phoneList', {
    templateUrl: 'phone-list/phone-list.template.html',
    controller: PhoneListController
  });
```

Lo que antes se hacia en el controladore, ahora es una tarea que se usa en el 
constructor de la clase. Las anotaciones de la injeccion de dependencias estan 
asociadas a la clase que usa una propiedad estatica `$inject`. En tiempo de 
ejecucion esto se transforma en la pripiedad `PhoneListController.$inject`.

La clase declara de forma adicional tres miembros: el array de telefonos, el 
nombre de key actual, y el parametro de busqueda. Estas son todas las cosas que
teniamos asociadas al controlador anteriormente pero no habiamos declarado de 
forma explicita. El ultimo de estos no es utilizado en el codigo TypeScript 
dado que solo es referenciado en el template, pero por un motivo de claridad es
bueno definir todos los miembros que nuestro controlador tendrá.

En el controlador de detalle de tendremos dos miembros: Unos para el telefono 
que el usuario esta viendo y otro para la url que se esta mostrando:

```
class PhoneDetailController {
  phone: any;
  mainImageUrl: string;
  static $inject = ['$routeParams', 'Phone'];
  constructor($routeParams: angular.route.IRouteParamsService, Phone: any) {
    let phoneId = $routeParams['phoneId'];
    this.phone = Phone.get({phoneId}, (phone: any) => {
      this.setImage(phone.images[0]);
    });
  }
  setImage(imageUrl: string) {
    this.mainImageUrl = imageUrl;
  }
}
angular.
  module('phoneDetail').
  component('phoneDetail', {
    templateUrl: 'phone-detail/phone-detail.template.html',
    controller: PhoneDetailController
  });
```

La tarea que antes se hacia en la funcion controlador, ahora se hace en el 
constructor de la funcion. Las anotaciones de dependencia de injeccion son 
asociadas a la clase usnaod la propiedad estatica $inject. En tiempo de 
ejecucion esto se convierte en una propiedad `PhoneListController.$inject`.

La clase declara tres miembros adicionales: un array de telefonos, el nombre de
 la clave de ordenamiento, y el parametro de busqueda. El ultimo de estos tres
no es usado en el codigo TypeScript y solo es referenciado en el template, pero
con el objetivo de la mayor claridad definimos todos los miembros que nuestro
controlador tendrá.

En el controlador de detalle de Phone tendremos dos miembros: uno para el Phone
que esta siendo utilizado por el usuario y otro para la URL de la image 
utilizada:

```
class PhoneDetailController {
  phone: any;
  mainImageUrl: string;
  static $inject = ['$routeParams', 'Phone'];
  constructor($routeParams: angular.route.IRouteParamsService, Phone: any) {
    let phoneId = $routeParams['phoneId'];
    this.phone = Phone.get({phoneId}, (phone: any) => {
      this.setImage(phone.images[0]);
    });
  }
  setImage(imageUrl: string) {
    this.mainImageUrl = imageUrl;
  }
}
angular.
  module('phoneDetail').
  component('phoneDetail', {
    templateUrl: 'phone-detail/phone-detail.template.html',
    controller: PhoneDetailController
  });
```

Esto hace que el codigo de nuestro controlador lusca mucho mas parecido a un 
controlador de Angular 2. Ahora tenemos todo listo para introducir Angular 2 en
el proyecto. 

Cualquier servicio que tengamos en Angular 1 en le projecto, será un buen 
candidato para convertirlo en una clase, dado que como los controladores, son 
tambien funciones constructoras. Pero solo tenemos la factoria Phone en el 
projecto, y es especial dado que es uan factoria `ngResource`. Por lo tanto no
haremos nada en el moento de la preparacion. Lo convertiremos directamente en 
un servicio Angular 2.

## Instalar Angular 2

Habiendo terminado el trabajo de preparapcion, empecemos el trabajo de 
actualizacion de PhoneCat. Haremos esto de forma incremental con la ayuda del
`module upgrade` que viene con Angular 2. Al momento en que terminemos, 
podremos remover Angular 1 completamente, pero la clave es hacerlo pieza a 
pieza sin romper la aplicacion.

> El proyecto tambien tiene algunas animaciones, que todavia no han sido 
actualizadas en esta version de la guia. Esto cambiará en una version futura.

Vamos a instalar Angular 2 en el proyecto, junto con SystemJS module loader.
Se puede revisar en la guia `QuickStart` para obtener las siguientes 
configuraciones de ahi:
* Agregar Angular 2 y cualquier otra nueva dependencia en `package.json`.
* El archivo de configuracion de SystemJS `systemjs.config.js` al raiz del 
proyecto.

Una vez que se dipone del paso anterior, ejecute:

```
  npm install
```

En brebe podremos cargar las dependencias de Angular 2 en la aplicacion via
`index.html`, pero antes debemos hacer algunos ajustes en los paths. Eso es
debido a que tendremos que leer algunos archivos desde `node_modules` y el raiz
 del proyecto, dado que antes todo era cargado desde el directorio `/app`.

Movemos el archivo `app/index.html` al raiz del projecto. Ahora cambiamos el 
raiz del proyecto de desarrollo en `package.json` a que apunte al raiz del 
proyecto tambien en lugar de `app`:

```
{
  "scripts": {
    "start": "http-server -a localhost -p 8000 -c-1 ./"
  }
}
```

Ahora podemos servir todo desde el raiz del proyecto hacia el navegador. Pero 
no queremos tener que cambiar todas las imagenes y los paths de datos en el
codigo de la aplicacion para que sea adecuado a al configuracin de development.
Por esta razon, agregaremos el tag `<base>` al `index.html` que generaran que 
las urls relativas resulvean hacia el directorio `/app`:

```
<base href="/app/">
```

Ahora podemos cargar Angular 2 mediante SystemJS. Agregarremos los polyfills de
 Angular 2 y el config de SystemJS al final de la seccion `<head>` y luego
usaremos `System.import` para cargar la aplicacion actual:

```
<script src="/node_modules/core-js/client/shim.min.js"></script>
<script src="/node_modules/zone.js/dist/zone.js"></script>
<script src="/node_modules/reflect-metadata/Reflect.js"></script>
<script src="/node_modules/systemjs/dist/system.src.js"></script>
<script src="/systemjs.config.js"></script>
<script>
  System.import('/app');
</script>
```

En el archivo `systemjs.config.js` que obtuvimos de la guia `Quickstart` 
tambien aremos algunos ajustes dada la estructura de nuestro proyecto. Queremos
 apuntar el navegador al raiz del proyecto cuando cargamos cosas por medio de
SystemJS, en lugar de usar la URL `<base>`:

```
System.config({
  paths: {
    // paths serve as alias
    'npm:': '/node_modules/'
  },
  map: {
    app: '/app',
    /* . . . */
  },
```

## Creado el *AppModule*

Ahora tenemos que crear la clase raiz llamada `AppModule` que contiene el 
`NgModule`. Ya existe un archivo llamado `app.module.ts` que contiene el modulo
de Angular 1. Lo renombramos a `app.module.ng1.ts` y actualizamos el 
correspondiente nombre en el archivo `index.html`. Este archvivo quedaria con 
el siguiente contenido:

```app.module.ng1.ts
'use strict';
// Define the `phonecatApp` Angular 1 module
angular.module('phonecatApp', [
  'ngAnimate',
  'ngRoute',
  'core',
  'phoneDetail',
  'phoneList',
]);
``` 

Ahora creamos un archivo nuevo `app.module.ts` con lo minimo necesario para la 
clase `NgModule`:

```app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
@NgModule({
  imports: [
    BrowserModule,
  ],
})
export class AppModule {}
```

## Inicilizacion hibrida 1+2 PhoneCat

Lo proximo que haremos es inicializar la aplicacion como una *aplicacion hibrida*
que soporte ambos, Angular 1 y componentes Angular 2. Una vez que hicimos esto, 
podemos empezar a convertir piezas individuales a Angular 2.

Para inicialiazr una aplicacion hibrida, primero debemos inicialiar el 
`UpgradeAdapter`, que proporciona el pegamento que conecta ambos frameworks. 
Vamos a importar la clase `UpgradeAdapter` en un archivo nuevo `app/main.ts`. 
El archivo se ha configurado como el punto de entrada de la aplicacion en 
`systemjs.config.js`, por lo tanto ya ha sido cargado en el navegador.

```app/main.ts
import { UpgradeAdapter } from '@angular/upgrade';
declare var angular: any;
import { AppModule } from './app.module';
```

> #### Porque declarar *angular* como *any*?
> Una referencia fija de `angular` hacia Angular 1 seria excelente. Pero no 
podemos importar la libreria `UMD typings`, `@types/angular`, sin importar
Angular 1 mismo por medio `import * as angular from 'angular'`.

> Angular 1 es cargado en el tag de script en `index.html` y migrar a ES6 no
lleva un esfuerzo considerable. Por lo tanto declarmos `angular` sin tipo
especifico y con tipo `any` para evitar errores.

Podemos crear un adaptador instalando la clase:

```
let upgradeAdapter = new UpgradeAdapter(AppModule);
```

Nuestra aplicacion es inicializada usando el tag `ng-app` de Angular 1 
actualmente attachado a `<html>` que se encuentra en la pagina host. Esto no 
functiona en Angular 2. Debemos cambiar a inicializar la aplicacion por medio
de inicializarla por javascript. Por lo tanto, tenemos que remover el tag 
`ng-app` de `index.html`, y agregamos lo siguiente a `main.ts`:

```
upgradeAdapter.bootstrap(document.documentElement, ['phonecatApp']);
```

El el adaptador usamos el elemento raiz de la aplicacion (que es el mismo 
elemento que usabamos en `ng-app` anteriormente) y los modulos de Angular 1 
que queremos cargar. Dado que estamos inicializando la aplicacoin por medio del
`UpgradeAdapter`, estamos ahora ejecutando una aplicacion hibrida Angular 1+2.

Esto significa que estamos ejecutando ambos, Angular 1 y 2, al mismo tiempo. 
Todavia no estamos ejecutando ningun componente Angular 2, ese es el siguiente 
paso. 

## Actualizar el servicio Phone

La primer pieza que vamos a migrar a Angular 2 es el servicio `Phone`, que 
recide en `app/code/phone/phone.service.ts` y hace posible para un componente 
cargar informacion de un phone desde el servidor. Ahora es implementado con 
`ngResource` y lo utilizamos para dos finalidades:
* Para cargar la lista de todos los phones en el componente phone list
* Para cargar el detalle de un phone en el componente phone detail

Podemos reeplazar esta implementacion con una clase Angular 2, manteniendo 
nuestro controlador en el lado de Angular 1.

En la nueva version, importamos el moduleo HTTP de Angular 2 y llamamos al 
servicio `Http` en lugar de `ngResource`.

Editamos el archivo `app.module.ts`, agregamos e importamos `HttpModule` al 
array de `imports` de `AppModule`:

```app.module.ts
import { HttpModule } from '@angular/http';
@NgModule({
  imports: [
    BrowserModule,
    HttpModule,
  ],
})
export class AppModule {}
```

Ahora estamos listos para actualizar el servicio `Phone` perse. Reemplazamos el
servicio en `phone.service.ts` basado en ngResource por una clase en TypeScript
decorada con `@Injectable`:

```app/core/phone/phone.service.ts(skeleton)
@Injectable()
export class Phone {
/* . . . */
}
```

El decorador `@Injectable` attachara metadata a la clase, informando a Angular
2 saber de sus dependencias. Como se describe en `Dependency Injection Guide`,
usamos este decorador cuando tenemos una clase que no tiene ningun otro 
decorador pero que al mismo tiempo sigue necesitando que se injecten sus 
dependencias.

En su contructor la clase espera recibir el servicio `Http`. Se le injecta a la
 clase y lo almacena como una clase privada. El servicio es usaod en sus dos 
metodos, uno carga la lista de todos los phones y el otro el detalle de una 
phone en particular:

```
@Injectable()
export class Phone {
  constructor(private http: Http) { }
  query(): Observable<PhoneData[]> {
    return this.http.get(`phones/phones.json`)
      .map((res: Response) => res.json());
  }
  get(id: string): Observable<PhoneData> {
    return this.http.get(`phones/${id}.json`)
      .map((res: Response) => res.json());
  }
}
```
Los metodos devuelves `Observables` de tipo `PhoneData` y `PhoneData[]`. Este
es un tipo que todavia no tenemos, por lo tanto agreguemos una interface:

```app/core/phone/phone.service.ts(interface)
export interface PhoneData {
  name: string;
  snippet: string;
  images: string[];
}
```

A continuacion se puede ver el codigo del servicio en su version Angular 2:

```app/core/phone/phone.service.ts
import { Injectable } from '@angular/core';
import { Http, Response } from '@angular/http';
import { Observable } from 'rxjs/Rx';
import 'rxjs/add/operator/map';
export interface PhoneData {
  name: string;
  snippet: string;
  images: string[];
}
@Injectable()
export class Phone {
  constructor(private http: Http) { }
  query(): Observable<PhoneData[]> {
    return this.http.get(`phones/phones.json`)
      .map((res: Response) => res.json());
  }
  get(id: string): Observable<PhoneData> {
    return this.http.get(`phones/${id}.json`)
      .map((res: Response) => res.json());
  }
}
```

Notece que estamos importantando el operador `map` desde RxJS `Observable` 
directamente. Tenemos que hacer esto para cada operador que querramos user en
Angular 2 dado que no los importa de forma automatica o por default.

El nuevo servicio `Phone` tiene las mismas funcionalidades que el original,
basado-en-`ngResource`. Dado que es un servicio Angular 2, lo tenemos que 
registrar con los proveedores en `NgModule`:

```app.module.ts
import { Phone } from './core/phone/phone.service';
@NgModule({
  imports: [
    BrowserModule,
    HttpModule,
  ],
  providers: [ Phone ]
})
export class AppModule {}
```

`UpgradeAdapter` tiene un metodo `downgradeNg2Provider` para conectar servicios
en Angular 2 y hacerlos disponibles en Angular 1. Se debe usar para conectar el 
servicio `Phone`:

```app/main.ts(expert)
import { Phone } from './core/phone/phone.service';

/* . . . */

angular.module('core.phone')
  .factory('phone', upgradeAdapter.downgradeNg2Provider(Phone));
```

Ahora que estamos cargando `phone.service.ts` por medio de un import que lo 
resuelve SystemJS, tenemos que **eliminar el tag <script>** del servicio que 
esta en `index.html`. Esto es algo que tenemos que ir haciendo con cada 
componente que vamos actualizando. A medida que vamos actualizando nuestro 
codigo desde Angualr 1 a Angular 2, tambien vamos migrando de scripts a 
modulos.

En este punto podemos hacer un intercambio entre nuestros dos components y usar
el nuevo servio en lugar del anterior. Lo injectamos como haciamos con la 
factory `phone` usando `$inject`, pero en realidad es una instancia de la clase
`Phone`, por lo tanto, podemos anotar su tipo de datos:

```app/phone-list/phone-list.component.ts
import { Phone, PhoneData } from '../core/phone/phone.service';
declare var angular: any;
class PhoneListController {
  phones: PhoneData[];
  orderProp: string;
  static $inject = ['phone'];
  constructor(phone: Phone) {
    phone.query().subscribe(phones => {
      this.phones = phones;
    });
    this.orderProp = 'age';
  }
}
angular.
  module('phoneList').
  component('phoneList', {
    templateUrl: 'app/phone-list/phone-list.template.html',
    controller: PhoneListController
  });
```

```app/phone-detail/phone-detail.component.ts
import { Phone, PhoneData } from '../core/phone/phone.service';
declare var angular: any;
class PhoneDetailController {
  phone: PhoneData;
  mainImageUrl: string;
  static $inject = ['$routeParams', 'phone'];
  constructor($routeParams: angular.route.IRouteParamsService, phone: Phone) {
    let phoneId = $routeParams['phoneId'];
    phone.get(phoneId).subscribe(data => {
      this.phone = data;
      this.setImage(data.images[0]);
    });
  }
  setImage(imageUrl: string) {
    this.mainImageUrl = imageUrl;
  }
}
angular.
  module('phoneDetail').
  component('phoneDetail', {
    templateUrl: 'phone-detail/phone-detail.template.html',
    controller: PhoneDetailController
  });
```

Ya tenemos dos componentes Angular 1 usando un servicio de Angular 2. Los 
componentes no necesitas saber acerca de la version del servicio, sin importar
que el servicio ahora devuelve un Obserable y no uns Promesa, cambio que es un 
gran avance. En cualquier caso, este paso nos ha permitido migrar un servicio
sin importar que version del framework lo consume.

> Podemos usar el metodo `toPromise` de un `Observable` para convertir 
Observables en Promises en el servicio.  Esto puede reducir la necesidad de 
cambios en controladores en en muchos casos.

 ## Actualizar componentes

 Siguiente paso, podemos actualizar nuestros componentes de Angular 1 a 
 componentes en Angular 2. Lo haremos de a uno por vez, mientras que 
 mantenemos la aplicacion en modo hibrido. Mientras realizamos estas 
 conversiones, tambien podmeos definir nuestros primeros Angular 2 *pipes*.

 Veamos el component phone list primero. Por el momento contiene una clase en 
 TypeScript que define un controlador y la definicion de un objecto. Podemos
 transformar esto en un componente renombrando la clase del controlador y 
 convirtiendo el componente de Angular 1 en un objeto de Angular 2 que utilice
 el decorador `@Component`. Tambien podemos remover la propiedad estatica 
 `$inject` de la clase:

 ```app/phone-list/phone-list.component.ts
 import { Component } from '@angular/core';
import { Phone, PhoneData } from '../core/phone/phone.service';
@Component({
  moduleId: module.id,
  selector: 'phone-list',
  templateUrl: 'phone-list.template.html'
})
export class PhoneListComponent {
  phones: PhoneData[];
  query: string;
  orderProp: string;
  constructor(phone: Phone) {
    phone.query().subscribe(phones => {
      this.phones = phones;
    });
    this.orderProp = 'age';
  }
}
```

El atributo `selector` es un selector CSS que define donde va a ir el 
componente en la pagina. En Angular 1 se realiza una equivalencia con el 
nombre del componente, pero en Angular 2 tenemos estos selectores explicitos. 
Esto equivaldrá con elementos con el nombre `phone-list`, igual que la version
Angular 1 lo hacia.

Tambien tenemos que convertir el template de este componente en la sintaxis 
valida de Angular 2. Los controles de search de Angular 1 `$ctrl` se expresan
en Angular 2 de dos vias con `[(ngModel)]`:

```
<p>
  Search:
  <input [(ngModel)]="query" />
</p>

<p>
  Sort by:
  <select [(ngModel)]="orderProp">
    <option value="name">Alphabetical</option>
    <option value="age">Newest</option>
  </select>
</p>
```

Reemplazamos en la lista el `ng-repeat` por `*ngFor` como describe la [pagina 
de sintaxis de templates](). Reemplazamos en la imagen el tag `ng-src` por el
atributo nativo `src`.

```app/phone-list/phone-list.template.html(phones)
<ul class="phones">
  <li *ngFor="let phone of getPhones()"
      class="thumbnail phone-list-item">
    <a href="/#!/phones/{{phone.id}}" class="thumb">
      <img [src]="phone.imageUrl" [alt]="phone.name" />
    </a>
    <a href="/#!/phones/{{phone.id}}" class="name">{{phone.name}}</a>
    <p>{{phone.snippet}}</p>
  </li>
</ul>
```

#### No existe *filter* o *orderBy* en Angular 2

En Angular 1 tenemos `filter` o `orderBy` predefinidos, pero estos no existen
en Angular 2, por lo tanto tenemos que hacer el filtrado y ordenamiento 
nosotros.

Reemplazamos `filter` y `orderBy` por medio de bindings a `getPhones()` a un
metodo del controlador, que implementa el filtrado y el la logica del 
ordenamiento dentro del componente mismo.

```app/phone-list/phone-list.component.ts
getPhones(): PhoneData[] {
  return this.sortPhones(this.filterPhones(this.phones));
}
private filterPhones(phones: PhoneData[]) {
  if (phones && this.query) {
    return phones.filter(phone => {
      let name = phone.name.toLowerCase();
      let snippet = phone.snippet.toLowerCase();
      return name.indexOf(this.query) >= 0 || snippet.indexOf(this.query) >= 0;
    });
  }
  return phones;
}
private sortPhones(phones: PhoneData[]) {
  if (phones && this.orderProp) {
    return phones
      .slice(0) // Make a copy
      .sort((a, b) => {
        if (a[this.orderProp] < b[this.orderProp]) {
          return -1;
        } else if ([b[this.orderProp] < a[this.orderProp]]) {
          return 1;
        } else {
          return 0;
        }
      });
  }
  return phones;
}
```

El nuevo `PhoneListComponent` usa la directiva `ngModel` de Angular 2, que es 
provisto por `FormsModule`. Agregamos `FormsModule` a los imports de `NgModule`
y declaramos el nuevo `PhoneListComponent`:

```app.module.ts
import { FormsModule } from '@angular/forms';
import { PhoneListComponent } from './phone-list/phone-list.component';
@NgModule({
  imports: [
    BrowserModule,
    HttpModule,
    FormsModule,
  ],
  declarations: [
    PhoneListComponent,
  ],
  providers: [ Phone ]
})
export class AppModule {}
```

En el archivo `main.ts` conectaremos el componente en el modulo Angular 1.

En lugar de recitrar un componente, registramos una directiva `phoneList`, una
version degradada del componente en Angular 2. El `UpgradeAdapter` crea un 
puente entre los dos:

```app/main.ts (excerpt)
import { PhoneListComponent } from './phone-list/phone-list.component';

/* . . . */

angular.module('phoneList')
  .directive(
    'phoneList',
    upgradeAdapter.downgradeNg2Component(PhoneListComponent) as angular.IDirectiveFactory
  );
```

El `as angular.IDirectiveFactory` indica al compilador de TypeScript que el
valor devuelto del metodo de degradacion es una fabrica de la dorectiva.

Debemos devolver del tag <script> el componente phone list de `index.html`.

Asi es como queda `phone-detail.component.ts`:

```app/phone-detail/phone-detail.component.ts
import { Component, Inject } from '@angular/core';
import { Phone, PhoneData } from '../core/phone/phone.service';
@Component({
  moduleId: module.id,
  selector: 'phone-detail',
  templateUrl: 'phone-detail.template.html',
})
export class PhoneDetailComponent {
  phone: PhoneData;
  mainImageUrl: string;
  constructor(@Inject('$routeParams')
                $routeParams: angular.route.IRouteParamsService,
              phone: Phone) {
    phone.get($routeParams['phoneId']).subscribe(phone => {
      this.phone = phone;
      this.setImage(phone.images[0]);
    });
  }
  setImage(imageUrl: string) {
    this.mainImageUrl = imageUrl;
  }
}
```

Esto es similar al componente phone list. Lo nuevo es el decorador `@Inject`
que identifica la dependencia `$routeParams`.

El injector de Angular 1 tiene una dependencia llamada `$routeParams` que fue 
injectado en `PhoneDetails` cuando todavia estaba en el ambito del controlador 
de Angular 1. Ahora intentamos injectarlo en el nuevo `PhoneDetailsComponent`.

Desafortunadamente, las dependencias en Angular 1 no esta disponibles de forma
automatica en los componentes de Angular 2. Debemos usar el `UpgradeAdapter` 
para hacer `$routeParams` un provedor en Angular 2. Hacemos esto en `main.ts`:

```app/main.ts ($routeParams)
upgradeAdapter.upgradeNg1Provider('$routeParams');
```

> No se debe registrar un proveedor Angular 1 en `NgModule`

Convertimos el componente de `phone detail` en sintaxis de Angular 2:

```app/phone-detail/phone-detail.template.html
<div *ngIf="phone">
  <div class="phone-images">
    <img [src]="img" class="phone"
        [ngClass]="{selected: img === mainImageUrl}"
        *ngFor="let img of phone.images" />
  </div>
  <h1>{{phone.name}}</h1>
  <p>{{phone.description}}</p>
  <ul class="phone-thumbs">
    <li *ngFor="let img of phone.images">
      <img [src]="img" (click)="setImage(img)" />
    </li>
  </ul>
  <ul class="specs">
    <li>
      <span>Availability and Networks</span>
      <dl>
        <dt>Availability</dt>
        <dd *ngFor="let availability of phone.availability">{{availability}}</dd>
      </dl>
    </li>
    <li>
      <span>Battery</span>
      <dl>
        <dt>Type</dt>
        <dd>{{phone.battery?.type}}</dd>
        <dt>Talk Time</dt>
        <dd>{{phone.battery?.talkTime}}</dd>
        <dt>Standby time (max)</dt>
        <dd>{{phone.battery?.standbyTime}}</dd>
      </dl>
    </li>
    <li>
      <span>Storage and Memory</span>
      <dl>
        <dt>RAM</dt>
        <dd>{{phone.storage?.ram}}</dd>
        <dt>Internal Storage</dt>
        <dd>{{phone.storage?.flash}}</dd>
      </dl>
    </li>
    <li>
      <span>Connectivity</span>
      <dl>
        <dt>Network Support</dt>
        <dd>{{phone.connectivity?.cell}}</dd>
        <dt>WiFi</dt>
        <dd>{{phone.connectivity?.wifi}}</dd>
        <dt>Bluetooth</dt>
        <dd>{{phone.connectivity?.bluetooth}}</dd>
        <dt>Infrared</dt>
        <dd>{{phone.connectivity?.infrared | checkmark}}</dd>
        <dt>GPS</dt>
        <dd>{{phone.connectivity?.gps | checkmark}}</dd>
      </dl>
    </li>
    <li>
      <span>Android</span>
      <dl>
        <dt>OS Version</dt>
        <dd>{{phone.android?.os}}</dd>
        <dt>UI</dt>
        <dd>{{phone.android?.ui}}</dd>
      </dl>
    </li>
    <li>
      <span>Size and Weight</span>
      <dl>
        <dt>Dimensions</dt>
        <dd *ngFor="let dim of phone.sizeAndWeight?.dimensions">{{dim}}</dd>
        <dt>Weight</dt>
        <dd>{{phone.sizeAndWeight?.weight}}</dd>
      </dl>
    </li>
    <li>
      <span>Display</span>
      <dl>
        <dt>Screen size</dt>
        <dd>{{phone.display?.screenSize}}</dd>
        <dt>Screen resolution</dt>
        <dd>{{phone.display?.screenResolution}}</dd>
        <dt>Touch screen</dt>
        <dd>{{phone.display?.touchScreen | checkmark}}</dd>
      </dl>
    </li>
    <li>
      <span>Hardware</span>
      <dl>
        <dt>CPU</dt>
        <dd>{{phone.hardware?.cpu}}</dd>
        <dt>USB</dt>
        <dd>{{phone.hardware?.usb}}</dd>
        <dt>Audio / headphone jack</dt>
        <dd>{{phone.hardware?.audioJack}}</dd>
        <dt>FM Radio</dt>
        <dd>{{phone.hardware?.fmRadio | checkmark}}</dd>
        <dt>Accelerometer</dt>
        <dd>{{phone.hardware?.accelerometer | checkmark}}</dd>
      </dl>
    </li>
    <li>
      <span>Camera</span>
      <dl>
        <dt>Primary</dt>
        <dd>{{phone.camera?.primary}}</dd>
        <dt>Features</dt>
        <dd>{{phone.camera?.features?.join(', ')}}</dd>
      </dl>
    </li>
    <li>
      <span>Additional Features</span>
      <dd>{{phone.additionalFeatures}}</dd>
    </li>
  </ul>
</div>
```

Hay varios cambios importantes aca:
* Se eliminó el prefijo `$ctrl` de todas las expresiones.
* De la misma manera que se hizo en `phone list`, reemplazamos `ng-src` con la 
propiedad estandar `src`.
* Se utiliza la sintaxis de binding alrrededor de `ng-class`. Angular 2 tiene 
una sintaxis [bastante similar]() `ngClass` a la que tiene Angular 1, su valor
no se evalua automaticamente como una expresion. En Angular 2 seimrpe se 
especifica en el template cuando el valor de un atributo es una expresion de un
atributo, en oposicion de una cadena literal.
* Reemplazamos `ng-repeat` por `*ngFor`.
* Reemplazamos `ng-click` con un binding de eventos de `click`.
* Se agrupa toda la vista en un `ngIf` que contola que se muestre el contenido
de la vista solo cuando se tiene un phone presente. Esto se necesita para se 
carga el componente por primera vez, no tenemos definido un `phone` todavia y 
las expresioens hacen referencia a un valor que todavia no existe. A diferencias
de Angular 1, en Angular 2, cuando una expresion falla genera un error cuando 
hace referencia a propiedades de objetos no definidos. Debemos ser explicitos 
cuando estas excepciones pueden presentarse.

Agregamos `PhoneDetailComponent` a las declaraciones `NgModule` y a `entryComponents`:

```app.module.ts
import { PhoneDetailComponent } from './phone-detail/phone-detail.component';
@NgModule({
  imports: [
    BrowserModule,
    UpgradeModule,
    HttpModule,
    FormsModule,
  ],
  declarations: [
    PhoneListComponent,
    PhoneDetailComponent,
  ],
  entryComponents: [
    PhoneListComponent,
    PhoneDetailComponent
  ],
  providers: [
    Phone,
    {
      provide: '$routeParams',
      useFactory: routeParamsFactory,
      deps: ['$injector']
    }
  ]
})
export class AppModule {
  ngDoBootstrap() {}
}
```

Debemos tambien quitar la linea de phone detail de las entradas de <script> en
`index.html`.

#### Agregar CHECKMARKPIPE

La directiva Angular 1 tienen un *filtro* `checkmark`. Vamos a convertilo en un
**pipe** de Angular 2.

No hay una actualizacion posible para convertir filtros en pipes. Es facil de 
convertir un filtro en su clase pipe equivalente. La implementacion es la misma
que antes, reempaquetarla en el metodo `transform`. Renombramos el archivo a
`checkmark.pipe.ts` para aplicar las convenciones de Angular 2:

```app/core/checkmark/checkmark.pipe.ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({name: 'checkmark'})
export class CheckmarkPipe implements PipeTransform {
  transform(input: boolean) {
    return input ? '\u2713' : '\u2718';
  }
}
```

Debemos importar y declarar el nuevo pipe creado y quitar el tag <script>
correspondiente en `index.html`:

```app.module.ts
import { CheckmarkPipe } from './core/checkmark/checkmark.pipe';
@NgModule({
  imports: [
    BrowserModule,
    UpgradeModule,
    HttpModule,
    FormsModule,
  ],
  declarations: [
    PhoneListComponent,
    PhoneDetailComponent,
    CheckmarkPipe
  ],
  entryComponents: [
    PhoneListComponent,
    PhoneDetailComponent
  ],
  providers: [
    Phone,
    {
      provide: '$routeParams',
      useFactory: routeParamsFactory,
      deps: ['$injector']
    }
  ]
})
export class AppModule {
  ngDoBootstrap() {}
}
``` 

### Migrando al router Angular 2 e inicializar

En este punto ya hemos reemplazado todos los componentes de Angular 1 con sus 
equivalentes de Angular 2.

La aplicacion todavia es inicializada como una aplicacion hibrida. Ya no se 
necesita este tipo de inicializacion. Es momento de quitar los ultimos rastros
de Angular 1 en dos pasos finales:

1. Cambiar al ruter de Angular 2
2. Inicializar como una aplicacion Angular 2 pura.

#### Cambiar al router de Angular 2

Angular 2 tiene un (router totalmente nuevo)[https://angular.io/docs/ts/latest/guide/router.html].

Como todo router, necesita un lugar en el UI para desplegar las vistas de las
rutas. En Angular 2 este trabajo es realizado por `<router-outlet>` y pertenece
a un *componente raiz* en el arbol de compomnentes de una aplicacion.

Todavia no tenemos este tipo de componente, dado que nuestra aplicacion todavia
es manejada por Angular 1. Debemos crear el archivo `app.component.ts` con la
clase `AppComponent`:

```app/app.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'phonecat-app',
  template: '<router-outlet></router-outlet>'
})
export class AppComponent {
}
```

Tiene una vista muy simple que solo contiene `<router-outlet>`. Este componente
renderea el contenido de la ruta actual y nada mas.

El selector le inidica a Angular 2 el poner este componente raiz dentro del 
elemento `<phonecat-app>` en la pagina ppal de la apliacion cuando se 
inicializa.

Debemos agregar el elemento `<phonecat-app>` al `index.html`. Este reemplaza la
directiva de Angular 1 `ng-view`:

```index.html (body)
  <body>
    <phonecat-app></phonecat-app>
  </body>
</html>
```

#### Creando el *routing module*

Un router necesita una configuracion sin importar si es Angular 1 o 2 o 
cualquier otro.

Los detales de la configuracion del router de Angular 2 se puede encontrar en 
la (documentacion del router)[https://angular.io/docs/ts/latest/guide/router.html]
que recomiendan crear un modulo `NgModule` dedicado a la configuracion del 
router (llamado un `Routing Module`):

```app/app.routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { APP_BASE_HREF, HashLocationStrategy, LocationStrategy } from '@angular/common';
import { PhoneDetailComponent } from './phone-detail/phone-detail.component';
import { PhoneListComponent }   from './phone-list/phone-list.component';
const routes: Routes = [
  { path: '', redirectTo: 'phones', pathMatch: 'full' },
  { path: 'phones',          component: PhoneListComponent },
  { path: 'phones/:phoneId', component: PhoneDetailComponent }
];
@NgModule({
  imports: [ RouterModule.forRoot(routes) ],
  exports: [ RouterModule ],
  providers: [
    { provide: APP_BASE_HREF, useValue: '!' },
    { provide: LocationStrategy, useClass: HashLocationStrategy },
  ]
})
export class AppRoutingModule {}
```
Este module define el objeto `routes` con las dos rutas a los dos componentes 
phone y una ruta default '/'. Envia `routes` al medodo `RouterModule.forRoot`
que realiza el resto.

Se habilitan las rutas con URLs de formato "hash" al estilo `#!/phones` en 
lugar de la estrategia "push state".

Actualizamos `AppModule` para importar `AppRoutingModule` y declarar el raiz 
`AppComponent`:

```app/app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule } from '@angular/forms';
import { HttpModule } from '@angular/http';
import { AppRoutingModule } from './app-routing.module';
import { AppComponent }     from './app.component';
import { CheckmarkPipe }    from './core/checkmark/checkmark.pipe';
import { Phone }            from './core/phone/phone.service';
import { PhoneDetailComponent } from './phone-detail/phone-detail.component';
import { PhoneListComponent }   from './phone-list/phone-list.component';
@NgModule({
  imports: [
    BrowserModule,
    FormsModule,
    HttpModule,
    AppRoutingModule
  ],
  declarations: [
    AppComponent,
    PhoneListComponent,
    CheckmarkPipe,
    PhoneDetailComponent
  ],
  providers: [
    Phone,
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule {}
```

El router de Angular 2 envia parametros diferente. Actualizamos el constructor 
del componente `PhoneDetail` para esperar y recibir un objeto `ActivatedRoute`.
Extraemos el `phoneId` de `ActivatedRoute.snapshot.params` y obtenemos los 
datos del telefono como antes:

```app/phone-detail/phone-detail.component.ts
import { Component }      from '@angular/core';
import { ActivatedRoute } from '@angular/router';
import { Phone, PhoneData } from '../core/phone/phone.service';
@Component({
  moduleId: module.id,
  selector: 'phone-detail',
  templateUrl: 'phone-detail.template.html'
})
export class PhoneDetailComponent {
  phone: PhoneData;
  mainImageUrl: string;
  constructor(activatedRoute: ActivatedRoute, phone: Phone) {
    phone.get(activatedRoute.snapshot.params['phoneId'])
      .subscribe((p: PhoneData) => {
        this.phone = p;
        this.setImage(p.images[0]);
      });
  }
  setImage(imageUrl: string) {
    this.mainImageUrl = imageUrl;
  }
}
```

#### Generar links para cada telefono
Ya no tenemos que setear manualmente los links al detalle de un telefono desde
la lista de estos. Podemos generar los links a cada `id` de los telefonos a la
directiva `routerLink` y dejando a la directiva construir la URL apropiada a
`PhoneDetailComponent`:

```app/phone-list/phone-list.template.html (list with links)
<ul class="phones">
  <li *ngFor="let phone of getPhones()"
      class="thumbnail phone-list-item">
    <a [routerLink]="['/phones', phone.id]" class="thumb">
      <img [src]="phone.imageUrl" [alt]="phone.name" />
    </a>
    <a [routerLink]="['/phones', phone.id]" class="name">{{phone.name}}</a>
    <p>{{phone.snippet}}</p>
  </li>
</ul>
```

#### Inicializando la aplicacion como Angular 2

Se puede notar una entrada de metadata adicional en `bootstrap` agregada al 
`AppModule`

```app/app.module.ts (booststrap)
bootstrap: [ AppComponent ]
```

Esto indica a Angular 2 que debe inicilizar la app con `AppComponent` como raiz
e insertar su vista en el host de la pagina.

Ahora cambiamos el metodo de inicializacionde `UpgradeAdapter` hacia la forma 
de Angular 2.

Ahora podemos remover `upgrade.bootstrap` de nuestra inicializacion de la 
aplicacion, y eliminamos el `ngDoBootstrap()` reemplazado por `app.module.ts`

```main.ts
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import { AppModule } from './app.module';
platformBrowserDynamic().bootstrapModule(AppModule);
```
```app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule } from '@angular/forms';
import { HttpModule } from '@angular/http';
import { AppRoutingModule } from './app-routing.module';
import { AppComponent }     from './app.component';
import { CheckmarkPipe }    from './core/checkmark/checkmark.pipe';
import { Phone }            from './core/phone/phone.service';
import { PhoneDetailComponent } from './phone-detail/phone-detail.component';
import { PhoneListComponent }   from './phone-list/phone-list.component';
@NgModule({
  imports: [
    BrowserModule,
    FormsModule,
    HttpModule,
    AppRoutingModule
  ],
  declarations: [
    AppComponent,
    PhoneListComponent,
    CheckmarkPipe,
    PhoneDetailComponent
  ],
  providers: [
    Phone,
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule {}
```

Ahora estamos ejecutando una aplicacion Angular 2 pura.

### Despidiendo a Angular 1

Ya es momento de remover todo rastro de Angular 1 en nuestra aplicacion. Solo
nos queda por quitar cualquier codigo que haga referencia.

Tenemos que quitar todas las referencias a `UpgradeMaodule` en `app.module.ts`,
como tambien cualquier service de Angular 1 del tipo (Factory provider)[https://angular.io/docs/ts/latest/guide/upgrade.html#making-angular-1-dependencies-injectable-to-angular-2].
tambien debemos remover cualquier ocurrencia de `downgradeComponent()` que 
encontremos, y tambien cualquier directiva que este declarada en Angular 1.

Debemos remover los siguiente archivos. Estos son modulos de configuracion que
no son necesarios en Angular 2:

* `app/app.module.ng1.ts`
* `app/app.config.ts`
* `app/core/core.module.ts`
* `app/core/phone/phone.module.ts`
* `app/phone-detail/phone-detail.module.ts`
* `app/phone-list/phone-list.module.ts`

Se pueden desinstalar los typings de Angular 1. Solo es necesario mantener los
referentes a Jasmine y Angular 2 polyfills.

> npm uninstall @types/angular @types/angular-animate @types/angular-cookies @types/angular-mocks @types/angular-resource @types/angular-route @types/angular-sanitize --save-dev

Finalmente, en `index.html`, eliminamos cualquier referencia a scripts de 
Angular 1, al upgrade module de Angular 2, y jQuery. Cuando terminamos debe
quedar de la siguiente manera:

```index.html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <base href="/app/">
    <title>Google Phone Gallery</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css" />
    <link rel="stylesheet" href="app.css" />
    <script src="/node_modules/core-js/client/shim.min.js"></script>
    <script src="/node_modules/zone.js/dist/zone.js"></script>
    <script src="/node_modules/reflect-metadata/Reflect.js"></script>
    <script src="/node_modules/systemjs/dist/system.src.js"></script>
    <script src="/systemjs.config.js"></script>
    <script>
      System.import('/app');
    </script>
  </head>
  <body>
    <phonecat-app></phonecat-app>
  </body>
</html>
```

Esta es la ultima vez que veremos Angular 1, ya podemos decirle adios.

### Apendice: Angualizando tests de PhoneCat

Los tests deben ser actualizados en este proceso tambien, y tambien pueden ser 
usados para asegurar este proceso de migracion. Los tests E2E son especialmente
utiles duranete este proceso.

#### E2E tests
El projecto PhoneCat tiene ambos, E2E Protractor y algunos tests de unidad. De
ambos, los tests E2E pueden ser tratados mucho mas facil: por definicion, los 
tests E2E acceden a nuestra aplicacion *por fuera* interactuando con los 
elementos que la aplicacion pone en la pantalla. Los tests E2E no tienen un 
alto concimiento de la estructura interna de los componentes de la aplicacion.
A pesar de todas las modificaciones efectuadas, nuestros tests E2E deberian 
funcionar con solo unas pequeñas modificaciones. Esto se debe a que no hemos
cambiado la aplicacion desde el punto de vista del usuario.

Durante la conversion TypeScript, no hay nada que debamos hacer para que los 
tests E2E sigan funcionando. Debemos hacer cambios cuando cambiamos la 
inicializacion a la forma hibrida.

El cambio debe ser realizado en el archivo `protractor-conf.js` para que 
sincronice con una aplicacion hibrida:

> ng12Hybrid: true

Los siguientes cambios se deben realizar cuando empezamos a actualizar 
componentes y sus vistas en Angular 2. Esto se debe a que los tests E2E tienen
matchers que son especificos de Angular 1. Para PhoneCat tenemos que hacer los
siguientes cambios para que las cosas funcionen con Angular 2:

Codigo Anterior                      Nuevo Codigo            Notas
`
by.repeater('phone in                by.css('.phones         El repetidor recide en 
$ctrl.phones').column('phone.name')  .name')                 Angular 1 ng-repeat
` 

`
by.repeater('phone in $ctrl.phones') by.css('.phones li')    El repetidor recide en
                                                             Angular 1 ng-model
`

`
by.model('$ctrl.query')              by.css('select')        El repetidor recide en
                                                             Angular 1 ng-model
`

`
by.model('$ctrl.orderProp')          by.css('h1')            El matcher del binding 
                                                             se aplica en el binding de datos
`

Cuando el metodo de inicializacion se cambia de `UpgradeModule` a una 
aplicacion de Angualr 2, Angular 1 deja de existir por completo. En este 
momento debemos decir a Protractor que no debe seguir buscando una aplicacion
Angular 1 y que debe buscar una aplicacion Angular 2.

Reemplazamos el `ng12Hybrid` que agregamos anteriormente en el archivo 
`protractor-conf.js`:

> useAllAngular2AppRoots: true,

En los tests de PhoneCat hay llamadas a la API de Protractor que hacen uso del 
servicio `$location` indirectamente. Y dado que este servicio no existe despues
de la actualizacion, debemos reemplazar estas llamadas con unas que usen 
llamadas genericas a URLs de la API de WebDriver. La primera de estas es la 
redicreccion spec:

```e2e-tests/scenarios.ts
it('should redirect `index.html` to `index.html#!/phones', function() {
  browser.get('index.html');
  browser.waitForAngular();
  browser.getCurrentUrl().then(function(url: string) {
    expect(url.endsWith('/phones')).toBe(true);
  });
});
```

Y la segunda es los links de phone spec:

```e2e-tests/scenarios.ts
it('should render phone specific links', function() {
  let query = element(by.css('input'));
  query.sendKeys('nexus');
  element.all(by.css('.phones li a')).first().click();
  browser.getCurrentUrl().then(function(url: string) {
    expect(url.endsWith('/phones/nexus-s')).toBe(true);
  });
});
```

#### Tests de unidad

Para los tests de unidad se necesita mayor trabajo para la migracion. En 
realidad se necesita *actualizar* en paralelo con el codigo de produccion.

Durante la conversion a TypeScript no hay que realizar ningun cambio. Pero 
puede ser de utilidad convertir el codigo a TypeScript, los mismos beneficios
que se aplican en el codigo de produccion se aplicaran a los tests.

Por ejemplo, en la spec del componente phone detail podemos usar las 
capacidades ES2015 como arrow functions y variables de scope, pero tambien la
definicion de tipos de algunos de los servicios que consumimos:

```app/phone-detail/phone-detail.component.spec.ts
describe('phoneDetail', () => {
  // Load the module that contains the `phoneDetail` component before each test
  beforeEach(angular.mock.module('phoneDetail'));
  // Test the controller
  describe('PhoneDetailController', () => {
    let $httpBackend: angular.IHttpBackendService;
    let ctrl: any;
    let xyzPhoneData = {
      name: 'phone xyz',
      images: ['image/url1.png', 'image/url2.png']
    };
    beforeEach(inject(($componentController: any,
                       _$httpBackend_: angular.IHttpBackendService,
                       $routeParams: angular.route.IRouteParamsService) => {
      $httpBackend = _$httpBackend_;
      $httpBackend.expectGET('phones/xyz.json').respond(xyzPhoneData);
      $routeParams['phoneId'] = 'xyz';
      ctrl = $componentController('phoneDetail');
    }));
    it('should fetch the phone details', () => {
      jasmine.addCustomEqualityTester(angular.equals);
      expect(ctrl.phone).toEqual({});
      $httpBackend.flush();
      expect(ctrl.phone).toEqual(xyzPhoneData);
    });
  });
});
```

Una vez que iniciamos el proceso de actualizacion e introducimos SystemJS,
necesitamos hacer algunos cambios para Karma. Necesitamos que SystemJS cargue
el codigo Angular 2 que puede realizarse con el siguiente codigo:

```karma-test-shim.js
// /*global jasmine, __karma__, window*/
Error.stackTraceLimit = 0; // "No stacktrace"" is usually best for app testing.
// Uncomment to get full stacktrace output. Sometimes helpful, usually not.
// Error.stackTraceLimit = Infinity; //
jasmine.DEFAULT_TIMEOUT_INTERVAL = 1000;
var builtPath = '/base/app/';
__karma__.loaded = function () { };
function isJsFile(path) {
  return path.slice(-3) == '.js';
}
function isSpecFile(path) {
  return /\.spec\.(.*\.)?js$/.test(path);
}
function isBuiltFile(path) {
  return isJsFile(path) && (path.substr(0, builtPath.length) == builtPath);
}
var allSpecFiles = Object.keys(window.__karma__.files)
  .filter(isSpecFile)
  .filter(isBuiltFile);
System.config({
  baseURL: '/base',
  // Extend usual application package list with test folder
  packages: { 'testing': { main: 'index.js', defaultExtension: 'js' } },
  // Assume npm: is set in `paths` in systemjs.config
  // Map the angular testing umd bundles
  map: {
    '@angular/core/testing': 'npm:@angular/core/bundles/core-testing.umd.js',
    '@angular/common/testing': 'npm:@angular/common/bundles/common-testing.umd.js',
    '@angular/compiler/testing': 'npm:@angular/compiler/bundles/compiler-testing.umd.js',
    '@angular/platform-browser/testing': 'npm:@angular/platform-browser/bundles/platform-browser-testing.umd.js',
    '@angular/platform-browser-dynamic/testing': 'npm:@angular/platform-browser-dynamic/bundles/platform-browser-dynamic-testing.umd.js',
    '@angular/http/testing': 'npm:@angular/http/bundles/http-testing.umd.js',
    '@angular/router/testing': 'npm:@angular/router/bundles/router-testing.umd.js',
    '@angular/forms/testing': 'npm:@angular/forms/bundles/forms-testing.umd.js',
  },
});
System.import('systemjs.config.js')
  .then(importSystemJsExtras)
  .then(initTestBed)
  .then(initTesting);
/** Optional SystemJS configuration extras. Keep going w/o it */
function importSystemJsExtras(){
  return System.import('systemjs.config.extras.js')
  .catch(function(reason) {
    console.log(
      'Warning: System.import could not load the optional "systemjs.config.extras.js". Did you omit it by accident? Continuing without it.'
    );
    console.log(reason);
  });
}
function initTestBed(){
  return Promise.all([
    System.import('@angular/core/testing'),
    System.import('@angular/platform-browser-dynamic/testing')
  ])
  .then(function (providers) {
    var coreTesting    = providers[0];
    var browserTesting = providers[1];
    coreTesting.TestBed.initTestEnvironment(
      browserTesting.BrowserDynamicTestingModule,
      browserTesting.platformBrowserDynamicTesting());
  })
}
// Import all spec files and start karma
function initTesting () {
  return Promise.all(
    allSpecFiles.map(function (moduleName) {
      return System.import(moduleName);
    })
  )
  .then(__karma__.start, __karma__.error);
}
```

Este codigo primero carga la configuracion de SystemJS, luego las librerias de
soporte de test de Angular 2, y luego los archivos specs de la aplicacion.

En la configuracion de Karma debe cambiarse a que utilice el directorio raiz 
como el directorio base, en lugar de `app`:

```karma.conf.js
basePath: './',
```

Una vez que hacemos esto podemos cargar SystemJS y otras dependencias, y se
cambia la configuracion para cargar los archivos de la aplicacion para que no
sean incluidos en la pagina por medio de Karma. Dejaremos que shim y SystemJS 
los carguen.

```karma.conf.js
// System.js for module loading
'node_modules/systemjs/dist/system.src.js',
// Polyfills
'node_modules/core-js/client/shim.js',
'node_modules/reflect-metadata/Reflect.js',
// zone.js
'node_modules/zone.js/dist/zone.js',
'node_modules/zone.js/dist/long-stack-trace-zone.js',
'node_modules/zone.js/dist/proxy.js',
'node_modules/zone.js/dist/sync-test.js',
'node_modules/zone.js/dist/jasmine-patch.js',
'node_modules/zone.js/dist/async-test.js',
'node_modules/zone.js/dist/fake-async-test.js',
// RxJs.
{ pattern: 'node_modules/rxjs/**/*.js', included: false, watched: false },
{ pattern: 'node_modules/rxjs/**/*.js.map', included: false, watched: false },
// Angular 2 itself and the testing library
{pattern: 'node_modules/@angular/**/*.js', included: false, watched: false},
{pattern: 'node_modules/@angular/**/*.js.map', included: false, watched: false},
{pattern: 'systemjs.config.js', included: false, watched: false},
'karma-test-shim.js',
{pattern: 'app/**/*.module.js', included: false, watched: true},
{pattern: 'app/*!(.module|.spec).js', included: false, watched: true},
{pattern: 'app/!(bower_components)/**/*!(.module|.spec).js', included: false, watched: true},
{pattern: 'app/**/*.spec.js', included: false, watched: true},
{pattern: '**/*.html', included: false, watched: true},
```

Dado que los templates HTML de los componentes de Angular 2 tambien seran 
cargados, necesitamos ayudar a Karma para que los pueda enrutar de forma correcta:

```karma.conf.js
// proxied base paths for loading assets
proxies: {
  // required for component assets fetched by Angular's compiler
  "/phone-detail": '/base/app/phone-detail',
  "/phone-list": '/base/app/phone-list'
},
```

Los archivos de test deben ser migrados a Angular 2 al mismo que el componente
original es migrado. El spec de pipe de checkmark es el mas sencillo de migrar
ya que no tiene dependencias:

```app/core/checkmark/checkmark.pipe.spec.ts
import { CheckmarkPipe } from './checkmark.pipe';
describe('CheckmarkPipe', function() {
  it('should convert boolean values to unicode checkmark or cross', function () {
    const checkmarkPipe = new CheckmarkPipe();
    expect(checkmarkPipe.transform(true)).toBe('\u2713');
    expect(checkmarkPipe.transform(false)).toBe('\u2718');
  });
});
```

Los tests de unidad del servicio phone es un poco mas complicado. Necesitamos
hacer un cambio desde Angular1 `$httpBackend` hacia Angular 2 Http Backend.

```app/core/phone/phone.service.spec.ts
import { inject, TestBed } from '@angular/core/testing';
import {
  Http,
  BaseRequestOptions,
  ResponseOptions,
  Response
} from '@angular/http';
import { MockBackend, MockConnection } from '@angular/http/testing';
import { Phone, PhoneData } from './phone.service';
describe('Phone', function() {
  let phone: Phone;
  let phonesData: PhoneData[] = [
    {name: 'Phone X', snippet: '', images: []},
    {name: 'Phone Y', snippet: '', images: []},
    {name: 'Phone Z', snippet: '', images: []}
  ];
  let mockBackend: MockBackend;
  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [
        Phone,
        MockBackend,
        BaseRequestOptions,
        { provide: Http,
          useFactory: (backend: MockBackend, options: BaseRequestOptions) => new Http(backend, options),
          deps: [MockBackend, BaseRequestOptions]
        }
      ]
    });
  });
  beforeEach(inject([MockBackend, Phone], (_mockBackend_: MockBackend, _phone_: Phone) => {
    mockBackend = _mockBackend_;
    phone = _phone_;
  }));
  it('should fetch the phones data from `/phones/phones.json`', (done: () => void) => {
    mockBackend.connections.subscribe((conn: MockConnection) => {
      conn.mockRespond(new Response(new ResponseOptions({body: JSON.stringify(phonesData)})));
    });
    phone.query().subscribe(result => {
      expect(result).toEqual(phonesData);
      done();
    });
  });
});
```

Para la spec del componente podemos mockear el servicio mismo `Phone`, y 
proveer datos de telefonos. Usamos la API de componentes de test de unidad de 
Angular.

```app/phone-detail/phone-detal.component.spec.ts
import { ActivatedRoute } from '@angular/router';
import { Observable } from 'rxjs/Rx';
import { async, TestBed } from '@angular/core/testing';
import { PhoneDetailComponent } from './phone-detail.component';
import { Phone, PhoneData } from '../core/phone/phone.service';
import { CheckmarkPipe } from '../core/checkmark/checkmark.pipe';
function xyzPhoneData(): PhoneData {
  return {
    name: 'phone xyz',
    snippet: '',
    images: ['image/url1.png', 'image/url2.png']
  };
}
class MockPhone {
  get(id: string): Observable<PhoneData> {
    return Observable.of(xyzPhoneData());
  }
}
class ActivatedRouteMock {
  constructor(public snapshot: any) {}
}
describe('PhoneDetailComponent', () => {
  beforeEach(async(() => {
    TestBed.configureTestingModule({
      declarations: [ CheckmarkPipe, PhoneDetailComponent ],
      providers: [
        { provide: Phone, useClass: MockPhone },
        { provide: ActivatedRoute, useValue: new ActivatedRouteMock({ params: { 'phoneId': 1 } }) }
      ]
    })
    .compileComponents();
  }));
  it('should fetch phone detail', () => {
    const fixture = TestBed.createComponent(PhoneDetailComponent);
    fixture.detectChanges();
    let compiled = fixture.debugElement.nativeElement;
    expect(compiled.querySelector('h1').textContent).toContain(xyzPhoneData().name);
  });
});
```
```app/phone-list/phone-list.compomnent.spec.ts
import { NO_ERRORS_SCHEMA } from '@angular/core';
import { ActivatedRoute } from '@angular/router';
import { Observable } from 'rxjs/Rx';
import { async, ComponentFixture, TestBed } from '@angular/core/testing';
import { SpyLocation } from '@angular/common/testing';
import { PhoneListComponent } from './phone-list.component';
import { Phone, PhoneData } from '../core/phone/phone.service';
class ActivatedRouteMock {
  constructor(public snapshot: any) {}
}
class MockPhone {
  query(): Observable<PhoneData[]> {
    return Observable.of([
      {name: 'Nexus S', snippet: '', images: []},
      {name: 'Motorola DROID', snippet: '', images: []}
    ]);
  }
}
let fixture: ComponentFixture<PhoneListComponent>;
describe('PhoneList', () => {
  beforeEach(async(() => {
    TestBed.configureTestingModule({
      declarations: [ PhoneListComponent ],
      providers: [
        { provide: ActivatedRoute, useValue: new ActivatedRouteMock({ params: { 'phoneId': 1 } }) },
        { provide: Location, useClass: SpyLocation },
        { provide: Phone, useClass: MockPhone },
      ],
      schemas: [ NO_ERRORS_SCHEMA ]
    })
    .compileComponents();
  }));
  beforeEach(() => {
    fixture = TestBed.createComponent(PhoneListComponent);
  });
  it('should create "phones" model with 2 phones fetched from xhr', () => {
    fixture.detectChanges();
    let compiled = fixture.debugElement.nativeElement;
    expect(compiled.querySelectorAll('.phone-list-item').length).toBe(2);
    expect(
      compiled.querySelector('.phone-list-item:nth-child(1)').textContent
    ).toContain('Motorola DROID');
    expect(
      compiled.querySelector('.phone-list-item:nth-child(2)').textContent
    ).toContain('Nexus S');
  });
  xit('should set the default value of orderProp model', () => {
    fixture.detectChanges();
    let compiled = fixture.debugElement.nativeElement;
    expect(
      compiled.querySelector('select option:last-child').selected
    ).toBe(true);
  });
});
```
Finalmente, necesitamos revisar los tests de componentes cuando cambiamos al
router de Angular 2. Para el componente de detalle tenemos que proveer un mock 
para el objeto de Angular 2 `ActivatedRoute` en lugar de usar `$routeParams` de
Angular 1:

```app/phone-detail/phone-detail.component.spec.ts
import { ActivatedRoute } from '@angular/router';
/* . . . */
class ActivatedRouteMock {
  constructor(public snapshot: any) {}
}
/* . . . */
  beforeEach(async(() => {
    TestBed.configureTestingModule({
      declarations: [ CheckmarkPipe, PhoneDetailComponent ],
      providers: [
        { provide: Phone, useClass: MockPhone },
        { provide: ActivatedRoute, useValue: new ActivatedRouteMock({ params: { 'phoneId': 1 } }) }
      ]
    })
    .compileComponents();
  }));
```

Y para el componente de lista necesitamos configurar algunas cosas para el 
router en si mismo para que la directiva del link funcione.

```app/phone-list/phone-list.component.spec.ts
import { NO_ERRORS_SCHEMA } from '@angular/core';
import { ActivatedRoute } from '@angular/router';
import { Observable } from 'rxjs/Rx';
import { async, ComponentFixture, TestBed } from '@angular/core/testing';
import { SpyLocation } from '@angular/common/testing';
import { PhoneListComponent } from './phone-list.component';
import { Phone, PhoneData } from '../core/phone/phone.service';
/* . . . */
  beforeEach(async(() => {
    TestBed.configureTestingModule({
      declarations: [ PhoneListComponent ],
      providers: [
        { provide: ActivatedRoute, useValue: new ActivatedRouteMock({ params: { 'phoneId': 1 } }) },
        { provide: Location, useClass: SpyLocation },
        { provide: Phone, useClass: MockPhone },
      ],
      schemas: [ NO_ERRORS_SCHEMA ]
    })
    .compileComponents();
  }));
  beforeEach(() => {
    fixture = TestBed.createComponent(PhoneListComponent);
  });
```

