# DWEC UT03: Modelo de objetos del documento (DOM) y gestión de eventos.

## Introducción a la gestión de eventos en JavaScript]

Hay que tener en cuenta que, sin eventos prácticamente no hay scripts. En casi todas las páginas web que incorporan JavaScript, suele haber eventos programados que disparan la ejecución de dichos scripts. La razón es muy simple, JavaScript fue diseñado para añadir interactividad a las páginas: el usuario realiza algo y la página reacciona.

También habrá algunos eventos que no están relacionados directamente con acciones de usuario: por ejemplo el evento de carga (load) de un documento, que se producirá automáticamente cuando un documento ha sido cargado.

Aquí hay una lista con los eventos del DOM más utilizados, solo para echar un vistazo:

Eventos del mouse:

* `click` – cuando el mouse hace click sobre un elemento (los dispositivos touch lo generan con un toque).
* `contextmenu` – cuando el mouse hace click derecho sobre un elemento.
* `mouseover/mouseout` – cuando el cursor del mouse ingresa/abandona un elemento.
* `mousedown/mouseup `– cuando el botón del mouse es presionado/soltado sobre un elemento.
* `mousemove` – cuando el mouse se mueve.

Eventos del teclado:

* `keydown/keyup` – cuando se presiona/suelta una tecla.

Eventos del elemento form:

* `submit` – cuando el visitante envía un `<form>`.
* `focus` – cuando el visitante hace foco en un elemento, por ejemplo un `<input>`.

Eventos del documento:

* `DOMContentLoaded` – cuando el HTML es cargado y procesado, el DOM está completamente construido

Eventos del CSS:

* `transitionend` – cuando una animación CSS concluye.

> Las lista de todos los eventos es muy extensa, podeis consultarla en direfentes páginas en internet, yo os dejo esta de ejemplo. [Elnace](https://omken.medium.com/javascript-events-cheat-sheet-9a4e840300b1).

## Reaccionando a eventos

Para reaccionar a los eventos podemos asignar un `handler`(controlador) el cual es una función que se ejecuta en caso de un evento. Los *handlers* son una forma de ejecutar código JavaScript en caso de acciones por parte del usuario.

Existen varias formas diferentes de manejar eventos en Javascript. Vamos a ver cada una de ellas, con sus particularidades.

| Forma | Ejemplo | 
|----------|----------|
| Mediante atributos HTML | `<button onClick="..."></button>` |
| Mediante propiedades Javascript | `boton.onclick = function() { ... }` |
| Mediante addEventListener() | `boton.addEventListener("click", ...)` |

### Mediante atributos HTML

Tambien conocido como **modelo de registro de eventos en línea**, el evento es añadido como un atributo más a la etiqueta HTML

```html
<a href="pagina.html" onClick="alert('Has pulsado en el enlace')">Pulsa aqui</a>
```
> **Evitar la acción por defecto.**
> 
> A veces es interesante el bloquear o evitar que se ejecute la acción por defecto. Por ejemplo, en nuestro caso anterior podríamos evitar que nos conecte con la nueva pagina.html. Cuando programamos un gestor de eventos, ese gestor podrá devolver un valor booleano true o false. Eso tendremos que programarlo con la instrucción return true|false. False quiere decir "no ejecutes la acción por defecto".
> ```html
> <a href="pagina.html" onClick="alert('Has pulsado en el enlace'); return false">Pulsa > > aqui</a>
> ```
> De esa forma, cada vez que pulsemos en el enlace realizará la llamada a la función `alert()` y cuando termine ejecutará la instrucción "return false", que le indicará al navegador que no ejecute la acción por defecto asignada a ese objeto (en este caso la acción por defecto de un hiperenlace es conectarnos con la página href de destino).
> También se puede utilizar el método `preventDefault()` del evento para lo mismo.

Toma en cuenta que dentro de `onClick` usamos comillas simples, porque el atributo en sí va entre comillas dobles. Si olvidamos que el código está dentro del atributo y usamos comillas dobles dentro, así: `onClick="alert("Has pulsado en el enlace")"`, no funcionará correctamente.

Un atributo HTML no es un lugar conveniente para escribir un montón de código, así que mejor creamos una función JavaScript y la llamamos allí.

```html
<script>
  function countRabbits() {
    for(let i=1; i<=3; i++) {
      alert("Conejo número " + i);
    }
  }
</script>

<input type="button" onClick="countRabbits()" value="¡Cuenta los conejos!">
```

Los nombres de los atributos HTML no distinguen entre mayúsculas y minúsculas, entonces `ONCLICK` funciona bien al igual que `onClick` y `onCLICK`.

Ahora sí, todo está un poco mejor organizado. Sin embargo, no es muy habitual tener bloques `<script>` de código Javascript en nuestro HTML, sino que lo habitual suele ser externalizarlo en ficheros `.js` para dividir y organizar mejor nuestro código.

```html
<!-- Etiqueta referenciando nuestro script externo con el código a ejecutar -->
<script src="script.js"></script>
<input type="button" onClick="countRabbits()" value="¡Cuenta los conejos!">
```

Gestionar eventos Javascript desde HTML es muy sencillo. Hay que tener en cuenta que "mezclamos" código Javascript dentro de HTML y esto no es recomendable (aunque en nuestro caso hayamos visto ejemplos sencillo hasta ahora). **Siempre que sea posible evitaremos esta manera de manejar eventos**.

### Mediante propiedades de los elementos

En este nuevo modelo el evento pasa a ser una propiedad de un elemento (objeto del DOM), de manera que seleccionando elementos desde el código del script podremos facilmente añadir una función a la propiedad en concreto.

```html
<input type="button" value="¡Cuenta los conejos!">
<script>
    const boton = document.querySelector('input[type="button"]');

    boton.onclick = () => {
        for (let i = 1; i <= 3; i++) {
            alert("Conejo número " + i);
        }
    }
</script>
```
Si el `handler` es asignado usando un atributo HTML entonces el navegador lo lee, crea una nueva función desde el contenido del atributo y lo escribe en la propiedad del DOM. Esta forma en realidad es la misma que ya habíamos visto antes.

Para eliminar un handler, asigna `elem.onclick = null`.

> #### *Tener en cuenta que ...*
> Si estás empezando a trabajar con eventos, por favor, nota algunas sutilezas. Podemos establecer una función existente como un `handler` pero la función debe ser asignada sin `()` ya que sino estaremos llamando a la ejecución de la propia función y no asignandola.
> ```js
> function countRabbits() {
>     for(let i=1; i<=3; i++) {
>       alert("Conejo número " + i);
>     }
>   }
> // correcto
> button.onclick = sayThanks;
> 
> // incorrecto
> button.onclick = sayThanks();
> ```
> 

Las mayúsculas en las propiedades DOM importan. Asignar un handler a `elem.onclick`, en lugar de `elem.ONCLICK`, ya que las propiedades DOM son sensibles a mayúsculas.

### Mediante `addEventListener()`

El problema fundamental de las formas ya mencionadas para asignar handlers es que no podemos asignar multiples handlers a un solo evento. Digamos que una parte de nuestro código quiere resaltar un botón al hacer click, y otra quiere mostrar un mensaje en el mismo click. Nos gustaría asignar dos handlers de eventos para eso. Pero una nueva propiedad DOM sobrescribirá la que ya existe.

```js
input.onclick = function() { alert(1); }
// ...
input.onclick = function() { alert(2); } //  el handler reemplaza el handler anterior
```

Los desarrolladores de estándares de la web entendieron eso hace mucho tiempo y sugirieron una forma alternativa de administrar los handlers utilizando los métodos especiales `addEventListener` y `removeEventListener`, que no tienen este problema y son mucho más potentes y versatiles para la mayoría de los casos.

La sintaxis es la siguiente.

```js
element.addEventListener(event, handler, [options]);
```

* `event`: Nombre del evento, por ejemplo: "click".
* `handler`: La función handler.
* `options`: Un objeto adicional para definir opciones (opcional).

Para eliminar el handler utilizaremos el metodo `removeEventListener` con la siguiente sintaxis.

```js
element.removeEventListener(event, handler, [options]);
```

> #### *Tener en cuenta que ...*
> Para remover un handler deberemos pasar exactamente la misma función que asignamos.
> ```js 
> elem.addEventListener( "click" , () => alert('¡Gracias!'));
> // ....
> elem.removeEventListener( "click", () => alert('¡Gracias!'));
> ```
> El handler no será removido porque `removeEventListener` obtiene otra función, con el mismo código, pero eso no importa, ya que es un objeto de función diferente.
> Aquí está la manera correcta:
> ```js 
> function handler() {
>   alert( '¡Gracias!' );
> }
>
> elem.addEventListener("click", handler);
> // ....
> elem.removeEventListener("click", handler);
> ```
> Por favor nota que si no almacenamos la función en una variable entonces no podremos removerla. No hay forma de “volver a leer” los handlers asignados por `addEventListener`.

Aplicaremos el mismo ejemplo anterior con este metodo.

```html
<input type="button" value="¡Cuenta los conejos!">
<script>
    const boton = document.querySelector('input[type="button"]');

    button.addEventListener("click", function() {
        alert("Hello!");
    });
</script>
```

Observa algunas cosas de este ejemplo:

* En el primer parámetro indicamos el nombre del evento, en nuestro ejemplo, `click`. Con `.addEventListener()` no se precede con `on` los nombres de eventos y se escriben en minúsculas, sin *camelCase*.
* En el segundo parámetro indicamos la función con el código que queremos que se ejecute cuando ocurra el evento.

Aunque es muy habitual escribir los eventos de esta forma, es posible que veas mucho más organizado este código si sacamos la función y la guardamos en una constante previamente, para luego hacer referencia a ella desde el `.addEventListener()`.

```js
const button = document.querySelector("button");
function action() {
  alert("Hello!");
};
button.addEventListener("click", action);
```

Una de las características más cómodas de utilizar `addEventListener` es que puedes añadir múltiples listeners de una forma muy sencilla.

```html
<input id="elem" type="button" value="Haz click en mí"/>

<script>
    function handler1() {
        alert('¡Gracias!');
    };

    function handler2() {
        alert('¡Gracias de nuevo!');
    }
    elem.addEventListener("click", handler1); // Gracias!
    elem.addEventListener("click", handler2); // Gracias de nuevo!
</script>
```
> Hay algunos eventos que no admiten ser configurados como propiedades y es obligatorio utilizar `addEventListener` como es `DOMContentLoaded`([enlace](https://developer.mozilla.org/en-US/docs/Web/API/Document/DOMContentLoaded_event)) aunque tambien podriamos utilizar el evento `load` que es ligeramente diferente ([enlace](https://developer.mozilla.org/en-US/docs/Web/API/Window/load_event)).
