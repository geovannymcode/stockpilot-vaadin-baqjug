# Sesión 8: Theming, CSS y marca propia

**Rama**: `sesion-8` (= `main`)
**Lo que vas a lograr**: que StockPilot se vea como un producto, no como
una demo: variantes de tema listas para usar, CSS propio para lo que las
variantes no cubren, y (opcional) tu propia paleta de marca, todo
construido en pasos chicos que puedes correr y mirar antes de seguir.

---

## Parte 1: Variantes de tema: estilo sin escribir CSS
Lo más rápido para darle personalidad a la UI son las variantes de tema:
estilos predefinidos que ya vienen con cada componente.

```java
saveButton.addThemeVariants(ButtonVariant.LUMO_PRIMARY);
```

!!! note "Esta línea va en el constructor, no en la declaración del campo"
    `addThemeVariants` necesita que el botón ya exista como objeto, así
    que se llama sobre la instancia, típicamente justo después de crearla
    o donde armes el layout de botones en el constructor de la vista. No
    funciona como parte de la declaración del campo (`private final
    Button saveButton = new Button("Guardar");`), porque ahí todavía no
    hay un lugar natural para encadenar la llamada sin ensuciar la
    declaración.

Agrégala y corre la aplicación: Guardar debería verse en azul sólido, un
cambio de una sola línea.

![Los botones con sus variantes de tema aplicadas](images/sesion8-theme-variants.png)

Con eso comprobado, agrega las otras dos:

```java
deleteButton.addThemeVariants(ButtonVariant.LUMO_ERROR, ButtonVariant.LUMO_TERTIARY);
addButton.addThemeVariants(ButtonVariant.LUMO_PRIMARY);
```

Y en el diálogo de confirmación de la Sesión 5, la variante se pasa como
texto, no como constante:

```java
dialog.setConfirmButtonTheme("error primary");
```

!!! abstract "Vaadin al paso: variantes de tema"
    Cada familia de componentes (`Button`, `TextField`, `Grid`...) trae un
    set de variantes propio, accesible como constantes de un enum
    (`ButtonVariant`, en este caso). `LUMO_PRIMARY` rellena el fondo del
    botón con el color principal del tema; `LUMO_ERROR` lo pinta de rojo;
    `LUMO_TERTIARY` le saca el borde. Puedes combinar varias a la vez, como
    en el botón Eliminar. Antes de escribir CSS a mano para algo, revisa si
    ya existe una variante: la documentación de cada componente en
    vaadin.com/docs las lista todas. El salto de "botón gris" a "botón azul
    sólido" con una sola línea es el mejor argumento a favor de este
    hábito.

Corre la aplicación de nuevo y confirma los tres botones. Con esto, las
variantes ya hicieron todo lo que pueden hacer: fíjate en lo que sigue sin
resolver. La barra de KPIs no tiene ni espaciado ni forma de tarjeta, y las
filas de stock bajo, marcadas desde la Sesión 3 con
`setPartNameGenerator`, todavía no tienen ningún color. Ninguna variante
de tema cubre eso: para eso escribimos CSS de verdad, en la Parte 2.

---

## Parte 2: CSS propio, para lo que las variantes no cubren
Todo lo que sigue va en `Application.java`, la clase que ya generó
start.vaadin.com en la raíz del proyecto (`com.baqjug.stockpilot`), tal
como quedó desde la Sesión 1:

```java
package com.baqjug.stockpilot;

import com.vaadin.flow.component.page.AppShellConfigurator;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application implements AppShellConfigurator {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

!!! danger "No crees una clase `AppShell` aparte"
    Si en algún momento leíste sobre Vaadin y viste una clase separada
    (algo como `AppShell.java implements AppShellConfigurator`) para
    configurar el tema, no la repliques acá. `Application` ya implementa
    `AppShellConfigurator`, y Vaadin solo permite **una** clase que lo
    haga en todo el proyecto. Si creas una segunda, la aplicación falla al
    arrancar con algo como esto:

    ```text
    Multiple classes implementing AppShellConfigurator were found.
    However, only a single class implementing AppShellConfigurator
    is allowed.
    ```

    La solución no es elegir cuál de las dos clases borrar: es no crear
    la segunda. Todo lo de esta sesión (cargar Lumo, cargar tu CSS) se
    agrega directamente sobre `Application`.

También vas a ver por ahí ejemplos con `@Theme("stockpilot")`. Esa
anotación está deprecada en Vaadin 25: el reemplazo es `@StyleSheet`, que
es justo lo que armamos en los siguientes tres momentos.

**Momento 1: cargar Lumo, explícitamente.**
Vaadin 25 cambió algo de fondo en cómo funciona el theming: los
componentes ya no traen Lumo (el tema base, el que define cada
`--lumo-*`) incorporado por defecto. Ahora se carga como cualquier otra
hoja de estilos, con una línea explícita:

```java
package com.baqjug.stockpilot;

import com.vaadin.flow.component.dependency.StyleSheet;
import com.vaadin.flow.component.page.AppShellConfigurator;
import com.vaadin.flow.theme.lumo.Lumo;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@StyleSheet(Lumo.STYLESHEET)
public class Application implements AppShellConfigurator {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

Corre la aplicación así, sin agregar nada más todavía. No debería verse
ningún cambio: Lumo ya estaba activo por otra vía, así que los botones,
campos y demás componentes se siguen viendo exactamente igual. El objetivo
de este paso no es ver algo distinto, es entender que a partir de ahora
Lumo se carga porque tu código lo pide, explícitamente, y no porque Vaadin
lo dé por hecho.

**Momento 2: el archivo vacío, y confirmar la conexión.**
Agrega la segunda anotación, siempre después de la de Lumo (el orden
importa: tu CSS tiene que cargar después del tema base, para poder
sobreescribirlo):

```java
@StyleSheet(Lumo.STYLESHEET)
@StyleSheet("styles.css")
public class Application implements AppShellConfigurator {
```

!!! danger "La ruta del archivo cambió, y si te equivocas no hay ningún error"
    Con `@Theme`, el CSS vivía en `frontend/themes/stockpilot/`. Con
    `@StyleSheet`, va en un lugar completamente distinto:

    ```
    src/main/resources/META-INF/resources/styles.css
    ```

    Si lo dejas en la ruta vieja, o en cualquier otra, la aplicación
    arranca sin problema, no aparece ningún error ni advertencia en la
    consola, y tu CSS simplemente no se aplica nunca. Es el peor tipo de
    falla para un taller en vivo: no hay nada que depurar porque no hay
    ningún síntoma más que "no pasó nada". Por eso el siguiente paso, una
    regla de una sola línea, es la manera de confirmar la conexión antes
    de escribir nada más.

Crea `src/main/resources/META-INF/resources/styles.css` con una sola
regla, deliberadamente tonta, solo para comprobar que el archivo se está
cargando:

```css
.app-name {
    color: red;
}
```

Reinicia el servidor (los cambios de tema a veces no aplican con hot
reload) y mira el nombre de la aplicación en el drawer. Si se puso rojo,
la conexión funciona. Si sigue con su color normal, algo está mal en la
ruta del archivo, y es mucho más fácil descubrirlo con una regla de una
línea que con las cuarenta que vienen en el momento 3.

!!! tip "Verificación de más peso: la pestaña Network"
    El color es rápido de ver, pero si quieres estar seguro sin depender
    de la vista, abre las herramientas de desarrollador del navegador, la
    pestaña Network, filtra por "styles.css", y recarga. Tiene que
    responder `200`. Un `404` ahí confirma exactamente lo que sospechas: el
    archivo no está donde Vaadin lo está buscando.

**Momento 3: el CSS de verdad, en tres bloques.**
Con la conexión confirmada, reemplaza la regla de prueba por el primer
bloque real: el nombre de la app con su estilo definitivo, y la barra y
las tarjetas de KPI.

```css
.app-name {
    font-weight: bold;
    font-size: 1.1rem;
}

.kpi-bar {
    gap: var(--lumo-space-m);
    padding: var(--lumo-space-m) 0;
    flex-wrap: wrap;
}

.kpi-card {
    background-color: var(--lumo-contrast-5pct);
    border-radius: var(--lumo-border-radius-l);
    padding: var(--lumo-space-m);
    display: flex;
    flex-direction: column;
    gap: var(--lumo-space-xs);
    min-width: 220px;
}
```

Cada tarjeta es un `Div` con dos `Span` adentro (el título y el valor):
sin `display: flex` y `flex-direction: column`, esos dos `Span` caerían
uno al lado del otro, pegados, porque ese es el flujo normal de dos
elementos en línea. `flex-direction: column` los apila; `gap` les pone aire
entre sí sin necesitar un margen manual en cada uno. En `.kpi-bar`, el
mismo `gap` separa una tarjeta de la siguiente, y `flex-wrap: wrap` deja
que las tarjetas bajen a una segunda fila en una pantalla angosta en vez
de desbordarse.

Corre la aplicación: la barra de KPIs ya tiene forma de tarjetas, en fila,
con separación.

Segundo bloque, la jerarquía tipográfica dentro de cada tarjeta:

```css
.kpi-title {
    font-size: var(--lumo-font-size-s);
    color: var(--lumo-secondary-text-color);
}

.kpi-value {
    font-size: var(--lumo-font-size-xxl);
    font-weight: bold;
}
```

El título chico y de un gris apagado, el valor grande y en negrita: el ojo
va directo al número, que es el dato que importa, y el título queda como
contexto secundario. Corre la aplicación y confirma la diferencia de
tamaño entre el título y el valor de cada tarjeta.

Tercer bloque, las filas de stock bajo. Este selector merece más
explicación que los anteriores, porque no es CSS normal:

```css
/* Esto NO funciona, y no hay ningún error que te avise */
.low-stock-row {
    background-color: var(--lumo-error-color-10pct);
}
```

```css
/* Esto sí funciona */
vaadin-grid::part(low-stock-row) {
    background-color: var(--lumo-error-color-10pct);
}
```

!!! abstract "Por qué `::part()`, y no una clase normal"
    `Grid` es un componente web que renderiza sus filas dentro de un
    shadow DOM: un árbol de elementos aparte, encapsulado, que el CSS de
    tu página no puede atravesar desde afuera. Escribir `.low-stock-row {
    }` en `styles.css` es CSS perfectamente válido, pero busca un elemento
    con `class="low-stock-row"` en el documento normal, y ese elemento no
    existe ahí: vive adentro del shadow DOM del grid, fuera del alcance de
    ese selector. Por eso no pasa nada, y por eso tampoco hay ningún error:
    para el navegador, la regla es válida, simplemente no encuentra a
    quién aplicársela.

    `setPartNameGenerator`, que escribiste en la Sesión 3, no le pone una
    clase CSS a la fila: le pone un atributo `part`, que es la manera en
    que un componente con shadow DOM expone deliberadamente ciertos
    elementos internos para que sí se puedan estilar desde afuera.
    `vaadin-grid::part(low-stock-row)` es un pseudo-elemento CSS diseñado
    exactamente para eso: "la parte de `vaadin-grid` marcada con
    `part="low-stock-row"`", cruzando el límite del shadow DOM por la
    puerta que el propio componente dejó abierta a propósito.

Reinicia el servidor y confirma que las filas de stock bajo aparecen en
rojo suave.

![Las filas de stock bajo resaltadas y las tarjetas de KPI con estilo](images/sesion8-css-propio.png)

!!! tip "Las variables `--lumo-*`"
    Lumo define sus colores, espaciados y tipografías como variables CSS
    (`--lumo-primary-color`, `--lumo-space-m`, etc.). Usarlas en tu propio
    CSS, en vez de valores fijos, hace que tus estilos respeten
    automáticamente el modo claro/oscuro y cualquier ajuste de marca que
    hagas más adelante.

Un último detalle, opcional pero prolijo: un poco de espacio arriba del
formulario de detalle:

```java
detailForm.getStyle().set("padding-top", "30px");
```

!!! abstract "Vaadin al paso: `getStyle()`"
    Todo componente tiene un método `getStyle()` que devuelve un objeto
    para aplicar propiedades CSS puntuales desde Java, sin necesidad de una
    clase CSS aparte. Es útil para ajustes de un solo componente; para
    estilos reutilizables entre varios, una clase CSS (como `kpi-card`)
    sigue siendo mejor.

---

## Parte 3: Tu propia paleta de marca (opcional)
Vaadin tenía un generador visual de temas, pero está pensado para Vaadin
10: produce `<dom-module>` y `<custom-style>`, dos mecanismos que ya no
existen en Vaadin 25. Vamos a hacer en su lugar lo que muestra la
documentación actual: redefinir las variables `--lumo-*` a mano,
directamente en `styles.css`.

Agrega este bloque al principio de `styles.css`, antes de las reglas que
ya escribiste en la Parte 2:

```css
html {
    --lumo-primary-color: hsl(173, 100%, 21%);
    --lumo-primary-color-50pct: hsla(173, 100%, 21%, 0.5);
    --lumo-primary-color-10pct: hsla(173, 100%, 21%, 0.1);
    --lumo-primary-text-color: hsl(173, 100%, 21%);
    --lumo-primary-contrast-color: #ffffff;
}
```

El color exacto no importa para este ejercicio, usa el que quieras. Guarda,
recarga la aplicación, y fíjate en tres lugares a la vez: el botón
Guardar, el botón `+` de la barra de productos, y el ítem activo del menú
lateral cambian de color, sin que hayas tocado una sola línea de Java ni
ninguna regla de la Parte 2.

Ese es el pago de una decisión que ya tomaste sin saberlo: `saveButton` y
`addButton` usan `ButtonVariant.LUMO_PRIMARY` desde la Parte 1, así que su
fondo sale de `--lumo-primary-color` y su texto de
`--lumo-primary-contrast-color`. El ítem activo del `SideNav` funciona
igual por defecto en Lumo: fondo con `--lumo-primary-color-10pct`, texto
con `--lumo-primary-text-color`. Ninguno de los tres sabe ni le importa
qué valor tienen esas variables hoy: cuando el valor cambia, ellos
cambian con él.

!!! tip "¿Y el resaltado de bajo stock?"
    Si comparas con atención, notarás que `vaadin-grid::part(low-stock-row)`
    no cambió de color. Es intencional: esa regla usa
    `--lumo-error-color-10pct` (Parte 2), no una variable de color
    primario. Es una advertencia semántica, no parte de tu marca, y no
    tendría sentido que cambiara solo porque cambiaste tu paleta. Separar
    "color de marca" de "color de estado" (error, éxito, advertencia) es
    justamente lo que te permite tener ambos a la vez sin que se pisen.

!!! abstract "Vaadin al paso: los sufijos `-50pct` y `-10pct`"
    `--lumo-primary-color-50pct` y `--lumo-primary-color-10pct` no son
    colores nuevos: son el mismo `--lumo-primary-color`, con la opacidad
    reducida al 50% y al 10%. Por eso en el bloque de arriba se escriben
    con `hsla(...)`, donde el cuarto valor es la opacidad. Lumo los usa
    para fondos sutiles, como el del ítem activo del menú, o para
    resaltados de foco y `:hover`.

!!! danger "Fondo y texto son variables distintas"
    `--lumo-primary-color` pinta fondos, como el del botón Guardar.
    `--lumo-primary-text-color` pinta texto, como el del ítem activo del
    menú. Son dos valores independientes: si solo redefines uno, el
    resultado queda a medias, con un color nuevo en el fondo y el azul
    por defecto de Lumo todavía en el texto (o al revés).

!!! note "La paleta por defecto cumple WCAG, la tuya no automáticamente"
    La paleta original de Lumo cumple el criterio de contraste de WCAG
    2.0 nivel AA (una relación de al menos 4.5:1 entre texto y fondo). Al
    reemplazar `--lumo-primary-color` o `--lumo-primary-text-color` con
    tus propios valores, esa garantía se pierde: nada impide elegir, por
    ejemplo, un texto blanco sobre un fondo amarillo pálido, ilegible
    para buena parte de tus usuarios. Verifica el contraste de tu paleta
    con una herramienta como el [contrast checker de
    WebAIM](https://webaim.org/resources/contrastchecker/) antes de
    darla por buena.

![Tu paleta de marca aplicada a StockPilot](images/sesion8-marca-propia.png)

Esto es apenas el color primario. Si quieres seguir explorando (tipografía,
radios de borde, colores de superficie y de contraste), la
[documentación de propiedades de color de
Lumo](https://vaadin.com/docs/latest/styling/lumo/lumo-style-properties/color)
lista todas las variables disponibles.

---

## Parte 4: El precio, también formateado en el Grid
Un último detalle suelto: en el Grid, el precio sale como `149900.00`,
mientras que en el formulario y en los KPIs ya se ve formateado como
moneda. `ProductListView` ya tiene el método que hace esa conversión,
`formatCurrency`, desde la Sesión 3: solo hace falta usarlo también en la
columna.

```java
grid.addColumn(row -> formatCurrency(row.unitPrice()))
        .setHeader("Precio")
        .setSortable(true)
        .setComparator(ProductRow::unitPrice);
```

!!! danger "Sin `setComparator`, el orden de la columna se rompe"
    `addColumn(ValueProvider<T, ?>)` con una función que devuelve un
    `String` (como esta, que arma el texto formateado) hace que el Grid
    ordene esa columna alfabéticamente por defecto, comparando el texto
    que ves, no el número que representa. `"$99.900"` puede terminar antes
    que `"$149.900"` en un orden alfabético, aunque numéricamente sea al
    revés. `setComparator(ProductRow::unitPrice)` le dice al Grid,
    explícitamente, que ordene por el `BigDecimal` real, no por el texto
    formateado que se ve en pantalla. Es un tropiezo clásico de cualquier
    columna formateada, no solo de esta: cada vez que le des a una columna
    un `ValueProvider` que devuelve texto derivado de un número, revisa si
    hace falta un `setComparator` al lado.

Corre la aplicación, ordena por precio, y confirma que el orden es
numérico y que los valores se ven con el mismo formato que el resto de la
aplicación.

---

## Cierre: StockPilot completo
Con esto, StockPilot tiene Grid con búsqueda reactiva, KPIs computados,
formulario validado, alta y baja, base de datos real, navegación entre dos
funcionalidades, y un tema propio, todo en Java, todo organizado por
funcionalidad de negocio.

```bash
git add .
git commit -m "sesion-8: theming, CSS y marca propia"
git branch sesion-8
git branch -f main sesion-8
```

Mira las [Referencias](referencias.md) para seguir profundizando.
