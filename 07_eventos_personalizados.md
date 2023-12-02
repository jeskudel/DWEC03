# DWEC UT03: Modelo de objetos del documento (DOM) y gestión de eventos.

## Eventos personalizados

En Javascript, al igual que existen varios eventos nativos que podemos escuchar mediante `.addEventListener()` como `click`, `keydown` o `mouseenter`, sería fantástico poder crear nuestros propios eventos para que se disparen automáticamente al ocurrir un suceso determinado. Eso es exactamente lo que son los **Custom Events** (eventos personalizados).

Las clases de eventos integradas forman una jerarquía, similar a las clases de elementos DOM. La raíz es la clase incorporada `Event`.

```js
let event = new Event(type[, options]);
```

Argumentos:

* `type` – tipo de evento, un string como "click" o nuestro propio evento como "mi-evento".

* `options` – el objeto con 3 propiedades opcionales:
  * `bubbles`: `true/false`, si es true, entonces el evento se propaga.
  * `cancelable`: `true/false`: si es true, entonces la “acción predeterminada” puede ser prevenida. Más adelante veremos qué significa para los eventos personalizados.
  * `composed`: indica si la propagación puede atravesar Shadow DOM o no.

  Por defecto, los dos son false: `{bubbles: false, cancelable: false}`.

> #### *Tener en cuenta que ...*
> En lugar de `Event` también se puede indicar CustomEvent (o alguno de sus objetos derivados). La diferencia radica en que `CustomEvent` se suele utilizar cuando queremos añadir datos personalizados, como vamos a hacer a continuación en las opciones.

```js
const MessageEvent = new CustomEvent("user:data-message", {
  detail: {
    from: "Usuario",
    message: "Hello world!"
  },
  bubbles: true,
});
```

### Disparar eventos personalizados

Los eventos nativos `Event` se suelen utilizar de forma interna por el navegador para crear un evento de navegador disparado por una acción real del usuario. Así pues, si el usuario pulsa en un `<button>`, el navegador dispara un evento `Event` que además, siendo más específicos es un evento `PointerEvent`.

Después de que se crea un objeto de evento, debemos “ejecutarlo” en un elemento usando la llamada `elem.dispatchEvent(event)`. Luego, los controladores reaccionan como si fuera un evento normal del navegador. Si el evento fue creado con la bandera `bubbles`, entonces se propaga.

En este ejemplo añadimos un manejador nativo para el `<button>` y simulamos otro evento nativo cuando nos situamos encima del `<span>`.

```html
<button>Click me</button>
<span class="text">Hover me</span>

<script>
const button = document.querySelector("button");
const text = document.querySelector(".text");

button.addEventListener("click", () => alert("Has pulsado el botón"));

text.addEventListener("mouseenter", () => {
  const event = new Event("click");
  button.dispatchEvent(event);
});
</script>
```

Al margen de este detalle, normalmente los `Event` se usan solamente para eventos reales del navegador. Si necesitamos controlar alguna acción determinada utilizaremos `CustomEvent`, asignándole un nombre de evento y personalizando su funcionamiento.

```js
const event = new Event("click", { detail: 123 });
event.detail    // undefined

const event = new CustomEvent("super-click", { detail: 123 });
event.detail    // 123
```

Por ejemplo, entre otras cosas, los CustomEvent permiten añadir información adicional al crear el objeto, mientras que el objeto Event no lo permite.

> #### *Tener en cuenta que ...*
> Hay una forma de diferenciar un evento de usuario “real” de uno generado por script. La propiedad `event.isTrusted` es `true` para eventos que provienen de acciones de usuarios reales y `false` para eventos generados por script.

Veamos un ejemplo de `bubbling` con un evento personalizado.

```html
<h1 id="elem">Hola desde el script!</h1>

<script>
  // Captura en document...
  document.addEventListener("hello", function (event) { // (1)
    alert(`Hola desde  ${event.target.tagName}.\nEstos son los ${event.detail}`); // Hola desde H1
  });

  // ...Envío en elem!
  let event = new CustomEvent("hello", { detail: "detalles del evento", bubbles: true }); // (2)
  elem.dispatchEvent(event);

  // el controlador del documento se activará y mostrará el mensaje.

</script>
```


* Debemos usar addEventListener para nuestros eventos personalizados, porque `on<event>` solo existe para eventos incorporados, `document.onhello` no funciona.
* Debes poner `bubbles:true`, de otra manera el evento no se propagará.

La mecánica de bubbling es la misma para los eventos integrados (`click`) y personalizados (`hello`). También hay etapas de captura y propagación.

### Eventos anidados

Usualmente los eventos se procesan en una cola. Por ejemplo: si el navegador está procesando `onclick` y ocurre un nuevo evento porque el ratón se movió, entonces el manejo de este último se pone en cola, y el controlador correspondiente `mousemove` será llamado cuando el procesamiento de `onclick` haya terminado.

La excepción notable es cuando un evento se inicia desde dentro de otro, por ejemplo, usando `dispatchEvent`. Dichos eventos se procesan inmediatamente: se llaman los nuevos controladores de eventos y luego se reanuda el manejo de eventos actual.

Por ejemplo, en el código siguiente, el evento `menu-open` se activa durante el `onclick`. Se procesa inmediatamente, sin esperar a que termine el controlador `onclick`.

```html
<button id="menu">Menu (dame clic)</button>

<script>
  menu.onclick = function() {
    alert(1);
    menu.dispatchEvent(new CustomEvent("menu-open", {
      bubbles: true
    }));
    alert(2);
  };
  // se dispara entre 1 y 2
  document.addEventListener('menu-open', () => alert('anidado'));
</script>
```