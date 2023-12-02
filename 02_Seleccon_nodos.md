# DWEC UT03: Modelo de objetos del documento (DOM) y gestión de eventos.

## Seleccionando nodos del DOM y acceso a propiedades y atributos

Cuando ya se ha construido automáticamente el árbol de nodos del DOM, ya podemos comenzar a utilizar sus funciones para acceder a cualquier nodo del árbol. El acceder a un nodo del árbol, es lo equivalente a acceder a un fragmento de la página de nuestro documento. Así que, una vez que hemos accedido a esa parte del documento, ya podemos modificar valores, crear y añadir nuevos elementos, moverlos de sitio, etc.

Para acceder a un nodo específico lo podemos hacer empleando dos métodos: o bien a través de los nodos padre, o bien usando un método de acceso directo. A través de los nodos padre partiremos del nodo raíz e iremos accediendo a los nodos hijo, y así sucesivamente hasta llegar al elemento que deseemos. Y para el método de acceso directo, que por cierto es el método más utilizado, emplearemos funciones del DOM, que nos permiten ir directamente a un elemento sin tener que atravesar nodo a nodo.

```html
<html>
<body>
  <section>Sección</section>

  <ul>
    <li>
      <b>Información</b>
    </li>
  </ul>
</body>
</html>
```

Si quisieramos selecciona el nodo `section` de la estructura anterior podríamos utilizar cualquiera de los siguientes metodos.

```js
let seccion = document.children[0].children[1].children[0]
let seccion2 = document.getElementsByTagName('section')[0]
```

### Selectores de elementos

En el tema anterior ya se introdujeron los selectores habituales para hacer selecciones directas de nodos, asi que, solo los nombraremos:

* .getElementById(id)
* .getElementsByClassName(class)
* .getElementsByName(value)
* .getElementsByTagName(tag)
* .querySelector(sel)	
* .querySelectorAll(sel)	

Recordar que los métodos modernos de `.querySelector...` utilizan selectores CSS para la especificación de los elementos a busca. También, recordar que los métodos que devuleven mas de un elemento, devuelven una collección y no un `array`.

> Aqui teneis la teoria que vimos en temas anteriores. [enlace](https://github.com/jeskudel/DWEC02/blob/master/01_Objetos_predefinidos_JS.md#metodos-tradicionales)

### Acceso a propiedades y atributos

En el tema anterior ya vimos como acceder a las propiedades de texto (y HTML también) para modificar sus valores, asi que, solo nombraremos las propiedades relacionadas con este aspecto.

* .nodeName
* .textContent
* .innerHTML
* .outerHTML

Recordar que tambien existe el metodo `setHTML()` que nos ayuda a introducir contenido HTML en algún nodo existente sin preocuparnos por posibles factores de seguridad como ocurriría si editamos la propiedad `innerHTML`.

Para acceder y editar atributos hay que tener en cuenta que existen atributos comunes a todas las etiquetas HTML, y atributos que sólo existen para determinadas etiquetas HTML. El orden de los atributos en HTML no es importante, da igual que este primero o segundo, no influye en nada.

Además, un atributo puede tener un valor o ser un atributo , es decir, simplemente estar presente y no tener ningún valor indicado:

```html
<div class="container" data-attr="value">
  <button disabled>Avisar</button>
</div>
```

> Aqui teneis la teoria que vimos en temas anteriores. [enlace](https://github.com/jeskudel/DWEC02/blob/master/01_Objetos_predefinidos_JS.md#modificando-contenido-de-elementos)

### Metodos de seleccion de atributos

Cuando el navegador carga la página, “lee” (o “parser”) el HTML y genera objetos DOM a partir de él. Para los nodos de `element`, la mayoría de los atributos HTML *estándar* se convierten automáticamente en propiedades de los objetos DOM.

Por ejemplo, si la etiqueta es `<body id="page">`, entonces el objeto DOM tiene `body.id="page"`.

Cuando el navegador analiza el HTML para crear objetos DOM para etiquetas, reconoce los atributos estándar y crea propiedades DOM a partir de ellos.

Entonces, cuando un elemento tiene `id` u otro atributo estándar, se crea la propiedad correspondiente. Pero eso no sucede si el atributo no es estándar.

```html
<body id="test" something="non-standard">
  <script>
    console.log(document.body.id);          // prueba
    // el atributo no estándar no produce una propiedad
    console.log(document.body.something);   // undefined
  </script>
</body>
```

Hay que tener en cuenta que para elemento un atributo estandar puede ser desconocido para otro elemento. Por ejemplo, `type` es estándar para `<input>` (HTMLInputElement), pero no para `<body>` (HTMLBodyElement). Los atributos estándar se describen en la especificación para la clase del elemento correspondiente.

Para averiguar si un atributo estandar existe como propiedad en un nodo `element` disponemos de los siguientes métodos:

| Método  | Descripción |
|----------|----------|
| `hasAttributes()` | Indica si el elemento tiene atributos HTML |
| `hasAttribute(attr)` | Indica si el elemento tiene el atributo HTML `attr` |
| `getAttributeNames()` | Devuelve un `array` con los atributos del elemento |
| `getAttribute(attr)` | Devuelve el valor del atributo `attr` del elemento o si no existe |

```html
<div id="page" class="info data dark" data-number="5"></div>
```
```js
const element = document.querySelector("#page");

element.hasAttributes();              // true (tiene 3 atributos)
element.hasAttribute("data-number");  // true (data-number existe)
element.hasAttribute("disabled");     // false (disabled no existe)

element.getAttributeNames();          // ["id", "data-number", "class"]
element.getAttribute("id");           // "page"
```

También se pueden leer todos los atributos usando `elem.attributes`: una colección de objetos que pertenecen a una clase integrada Attr, con propiedades nombre y valor .

```html
<body>
  <div id="elem" about="Elephant" class="container"></div>

  <script>
    const elem = document.querySelector("#elem");
    console.log(elem.getAttribute('about')); 
    for (let attr of elem.attributes) { 
      console.log(`${attr.name} = ${attr.value}`);
    }
  </script>
</body>
```

### Modificando atributos

Por otro lado, tenemos algunos métodos para modificar atributos HTML existentes, o directamente, eliminarlos:

| Método  | Descripción |
|----------|----------|
| `setAttribute(attr, value)` | Añade o cambia el atributo `attr` al valor `value` del elemento HTML |
| `toggleAttribute(attr, force)` | IAñade atributo `attr` si no existe, si existe lo elimina |
| `removeAttribute(attr)` | Elimina el atributo `attr` del elemento HTML |

```html
<div id="page" class="info data dark" data-number="5"></div>
```
```js
const element = document.querySelector("#page");

element.setAttribute("data-number", "10");   // Cambiar data-number a 10
element.removeAttribute("id");               // Elimina el atributo id
element.setAttribute("id", "page");          // Vuelve a añadirlo
```

Hay que hablar de un caso especial, que es el que comentamos en el que podemos establecer atributos HTML que son `boolean`, es decir, que no tienen indicado ningún valor.

Si esto lo hacemos con el método `setAttribute()` y le indicamos un booleano, no tendremos exactamente lo que buscamos. Recuerda que los atributos HTML son siempre de tipo :

```js
const button = document.querySelector("button");

button.setAttribute("disabled", true);   // ❌ <button disabled="true">Clickme!</button>
button.disabled = true;                  // ✅ <button disabled>Clickme!</button>
button.setAttribute("disabled", "");     // ✅ <button disabled>Clickme!</button>
```
Por lo tanto, la forma correcta de establecerlos es indicar un valor vacío. Automáticamente, el navegador sabrá que una cadena de texto vacía es un booleano y ocultará su valor. Por otro lado, si lo haces mediante una propiedad Javascript, si puedes usar un booleano, y añadirá el atributo HTML automáticamente.
