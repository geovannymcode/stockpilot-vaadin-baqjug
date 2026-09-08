# Sesión 7: Navegación y una segunda feature

**Rama**: `sesion-7`
**Lo que vas a lograr**: un menú de navegación entre dos pantallas, y la
comprobación práctica de la promesa de package-by-feature: agregar una
funcionalidad nueva sin tocar la que ya existe.

Esta sesión se arma en bloques pequeños, cada uno con algo para correr y
mirar antes de pasar al siguiente, en vez de dos archivos completos con la
explicación después. También cambia el orden en el que los creamos: en
vez de escribir primero `MainLayout` (que depende de una vista que todavía
no existe), empezamos por una versión mínima de esa vista, para que nunca
haya un momento con la compilación rota.

---

## Parte 1: `DashboardView`, la versión mínima
Vamos a crear un paquete completamente nuevo, `dashboard/`, con una vista
de inicio que más adelante va a resumir el estado del inventario. Por
ahora, la versión más chica posible: solo para tener algo que compile y
para que puedas navegar a ella en un rato.

`dashboard/DashboardView.java`:

```java
package com.baqjug.stockpilot.dashboard;

import com.vaadin.flow.component.html.H2;
import com.vaadin.flow.component.orderedlayout.VerticalLayout;
import com.vaadin.flow.router.Route;

@Route("")
public class DashboardView extends VerticalLayout {

    public DashboardView() {
        setSizeFull();
        add(new H2("Bienvenido a StockPilot"));
    }
}
```

Con esto acabamos de crear, a propósito, una colisión: `DashboardView`
tiene `@Route("")`, y `ProductListView`, desde la Sesión 2, también.
Todavía no corras la aplicación. La resolvemos ya mismo, en la Parte 2,
antes de tocar nada más.

---

## Parte 2: La colisión de rutas
Dos vistas no pueden compartir la misma ruta. Si arrancaras la aplicación
en este momento, con `DashboardView` y `ProductListView` compitiendo por
`@Route("")`, Vaadin fallaría al levantar con algo parecido a esto:

```text
com.vaadin.flow.server.InvalidRouteConfigurationException: Navigation
targets must have unique routes, found navigation targets
'com.baqjug.stockpilot.dashboard.DashboardView' and
'com.baqjug.stockpilot.product.ProductListView' with the same route.
```

Es un error al arrancar, no una excepción mientras usas la app: el
servidor ni siquiera termina de levantar. Si alguna vez te encontrás con
un mensaje así, meses después de esta guía, agregando una vista propia, ya
sabes qué buscar: dos `@Route` con el mismo valor.

La solución es mover `ProductListView` a su propia ruta, porque la raíz
(`""`) ahora le pertenece al dashboard:

```java
@Route("products")   // antes era @Route("")
public class ProductListView extends VerticalLayout {
```

Con este cambio ya puedes correr la aplicación: vas a ver la
`DashboardView` mínima en la raíz. Todavía no hay menú para llegar a
`/products`, así que pruébalo escribiendo la URL a mano en el navegador.
El menú es justo lo que sigue.

---

## Parte 3: El App Shell, en tres pasos
Ahora que `DashboardView` existe, `MainLayout` puede compilar. Lo
construimos en tres pasos, cada uno con algo para correr y mirar.

Esto es transversal a toda la app: no es "de producto" ni de ninguna otra
funcionalidad de negocio, así que va en el paquete técnico que reservamos
desde la Sesión 1: `shell/`.

**Paso 1: la cáscara vacía.**

```java
package com.baqjug.stockpilot.shell;

import com.vaadin.flow.component.applayout.AppLayout;
import com.vaadin.flow.router.Layout;

@Layout
public class MainLayout extends AppLayout {
}
```

!!! abstract "Vaadin al paso: `@Layout` y `AppLayout`"
    `AppLayout` es el componente base para una cáscara de aplicación, con
    cabecera y/o menú lateral. `@Layout` le dice a Vaadin que envuelva
    todas las vistas registradas con `@Route` dentro de esta cáscara,
    automáticamente, sin que cada vista tenga que declararlo. Eso acaba de
    pasar: `DashboardView` y `ProductListView` ya están las dos dentro de
    un `MainLayout`, aunque esa cáscara todavía no tenga nada adentro.

Corre la aplicación. No vas a notar ningún cambio, y así tiene que ser: la
cáscara ya envuelve las dos vistas, pero está vacía, así que no hay nada
que se vea distinto todavía. Confirma nada más que sigue compilando y
arrancando sin errores; el cambio visible llega en el paso 2.

**Paso 2: la cabecera y el drawer.**

```java
@Layout
public class MainLayout extends AppLayout {

    public MainLayout() {
        setPrimarySection(Section.DRAWER);

        H1 appName = new H1("StockPilot");
        appName.addClassName("app-name");

        HorizontalLayout header = new HorizontalLayout(appName);
        header.setPadding(true);
        addToDrawer(header);
    }
}
```

!!! abstract "Vaadin al paso: `Section.DRAWER` contra `Section.NAVBAR`"
    `AppLayout` soporta dos secciones primarias: `NAVBAR`, una barra
    horizontal arriba (la opción por defecto), y `DRAWER`, un panel
    lateral replegable. `setPrimarySection(Section.DRAWER)` elige el panel
    lateral en vez de la barra superior: es la diferencia entre un menú
    horizontal arriba y uno vertical a la izquierda, que en pantallas
    angostas se puede colapsar detrás de un botón de hamburguesa.
    `addToDrawer(...)` agrega contenido específicamente a esa sección.

Corre la aplicación: ahora sí hay algo que ver, un panel a la izquierda
con el nombre "StockPilot". Todavía no navega a ningún lado: eso es lo que
falta en el paso 3.

**Paso 3: el menú de navegación.**

```java
public MainLayout() {
    setPrimarySection(Section.DRAWER);

    H1 appName = new H1("StockPilot");
    appName.addClassName("app-name");

    HorizontalLayout header = new HorizontalLayout(appName);
    header.setPadding(true);
    addToDrawer(header);

    SideNav nav = new SideNav();
    nav.addItem(new SideNavItem("Inicio", DashboardView.class, VaadinIcon.HOME.create()));
    nav.addItem(new SideNavItem("Productos", ProductListView.class, VaadinIcon.PACKAGE.create()));
    addToDrawer(nav);
}
```

`MainLayout` completo, con todos los imports que necesita a esta altura:

```java
package com.baqjug.stockpilot.shell;

import com.baqjug.stockpilot.dashboard.DashboardView;
import com.baqjug.stockpilot.product.ProductListView;
import com.vaadin.flow.component.applayout.AppLayout;
import com.vaadin.flow.component.html.H1;
import com.vaadin.flow.component.icon.VaadinIcon;
import com.vaadin.flow.component.orderedlayout.HorizontalLayout;
import com.vaadin.flow.component.sidenav.SideNav;
import com.vaadin.flow.component.sidenav.SideNavItem;
import com.vaadin.flow.router.Layout;
```

!!! abstract "`SideNavItem` recibe una clase, no una URL, y eso importa"
    `new SideNavItem("Productos", ProductListView.class, icono)` no le da
    a Vaadin la URL `"/products"` escrita a mano: le da la clase
    `ProductListView`. Internamente, Vaadin resuelve la ruta a partir de
    la anotación `@Route` de esa clase en el momento de navegar, no cuando
    armas el menú. La consecuencia concreta: si el mes que viene cambias
    `@Route("products")` por `@Route("catalogo")`, el menú sigue
    funcionando sin que lo toques, porque en ningún lado del código del
    menú quedó un string `"/products"` escrito a mano que se te pueda
    desactualizar. Este ítem es refactor-safe: renombrar una ruta es un
    cambio de una línea en un archivo, no una búsqueda de texto por todo
    el proyecto.

Corre la aplicación y navega entre "Inicio" y "Productos" con el menú.
Este es el primer momento en que las dos pantallas se sienten como una
sola aplicación.

---

## Parte 4: Enriquecer el dashboard con sus KPIs
Con la navegación funcionando, volvemos a `DashboardView` para que haga lo
que prometía: resumir el inventario. Primero el componente visual, después
los números.

El helper de la tarjeta. Ya escribiste esta forma en la Sesión 3, para los
KPIs del Grid; acá la extraemos a un método propio porque el dashboard va
a necesitar tres tarjetas, no dos:

```java
private Div kpiCard(String title, String value) {
    Span titleSpan = new Span(title);
    titleSpan.addClassName("kpi-title");
    Span valueSpan = new Span(value);
    valueSpan.addClassName("kpi-value");
    Div card = new Div(titleSpan, valueSpan);
    card.addClassName("kpi-card");
    return card;
}
```

!!! note "Las mismas clases CSS de la Sesión 3, sin estilo todavía"
    `kpi-card`, `kpi-title`, y `kpi-bar` (el contenedor que agrupa varias
    tarjetas en fila, que usamos abajo) son las mismas clases que ya
    usaste en el panel de KPIs del catálogo, en la Sesión 3, y siguen sin
    tener una regla de CSS que las pinte: eso llega recién en la Sesión 8.
    Reusarlas acá no es casualidad: es el mismo tipo de componente visual
    en dos pantallas distintas, así que comparte el mismo estilo el día
    que ese estilo exista, sin que tengas que escribir CSS nuevo por cada
    pantalla.

Ahora los números. Inyectamos `ProductService` en el constructor, igual
que hicimos con `ProductListView` en la Sesión 6:

```java
package com.baqjug.stockpilot.dashboard;

import com.baqjug.stockpilot.product.Product;
import com.baqjug.stockpilot.product.ProductService;
import com.vaadin.flow.component.html.Div;
import com.vaadin.flow.component.html.H2;
import com.vaadin.flow.component.html.Span;
import com.vaadin.flow.component.orderedlayout.HorizontalLayout;
import com.vaadin.flow.component.orderedlayout.VerticalLayout;
import com.vaadin.flow.router.Route;

import java.math.BigDecimal;
import java.text.NumberFormat;
import java.util.List;
import java.util.Locale;

@Route("")
public class DashboardView extends VerticalLayout {

    public DashboardView(ProductService productService) {
        setSizeFull();
        add(new H2("Bienvenido a StockPilot"));

        List<Product> products = productService.findAll();
        BigDecimal totalValue = products.stream()
                .map(p -> p.getUnitPrice().multiply(BigDecimal.valueOf(p.getStockQuantity())))
                .reduce(BigDecimal.ZERO, BigDecimal::add);
        long lowStock = products.stream().filter(Product::isLowStock).count();

        Div totalCard = kpiCard("Valor total del inventario", formatCurrency(totalValue));
        Div countCard = kpiCard("Productos en catálogo", String.valueOf(products.size()));
        Div lowStockCard = kpiCard("Stock bajo", lowStock + " producto(s)");

        HorizontalLayout kpis = new HorizontalLayout(totalCard, countCard, lowStockCard);
        kpis.addClassName("kpi-bar");
        add(kpis);
    }

    private Div kpiCard(String title, String value) {
        // igual que arriba
    }

    private String formatCurrency(BigDecimal amount) {
        NumberFormat format = NumberFormat.getCurrencyInstance(Locale.of("es", "CO"));
        format.setMaximumFractionDigits(0);
        return format.format(amount);
    }
}
```

!!! danger "`Locale.of(...)`, no `new Locale(...)`"
    El constructor `new Locale("es", "CO")` está deprecado desde Java 19:
    todavía funciona, pero el compilador te va a marcar la advertencia.
    `Locale.of("es", "CO")` es el reemplazo, y es el que ya usas en el
    resto de la guía desde hace varias sesiones; esta era la última
    aparición de la forma vieja que quedaba.

`productService.findAll()` sigue devolviendo `List<Product>`, la lista
completa de entidades, tal como quedó definida en la Sesión 6:
`DashboardView` no pasó por el cambio a `ProductRow` que hicimos ahí,
porque ese cambio resolvía un problema de Signals dentro del Grid, y acá
no hay ningún Grid ni ningún signal. `Product::isLowStock` también sigue
disponible en la entidad, sin cambios. Nada de lo que ajustamos en la
Sesión 6 le rompe algo a esta vista.

!!! abstract "Por qué `ProductService`, nunca `ProductRepository`"
    `DashboardView` vive en `dashboard/`, una funcionalidad distinta de
    `product/`, y solo puede depender de lo que `product/` expone como
    pública: `ProductService`. Nunca de `ProductRepository`. La
    consecuencia concreta de saltarse esta regla: si `DashboardView`
    llamara a `ProductRepository` directamente, cualquier cambio en cómo
    `product/` consulta sus datos (una columna nueva, un `@Query`
    distinto, el día de mañana cambiar de JPA a otra cosa) habría que
    replicarlo, o por lo menos revisarlo, en dos lugares en vez de uno. La
    feature de producto dejaría de poder cambiar sus entrañas sin
    arriesgarse a romper al dashboard. `ProductService` es el contrato;
    `ProductRepository` es apenas la implementación de ese contrato, y las
    implementaciones no se comparten entre features.

!!! note "Por qué esta vista no usa Signals"
    El dashboard hace una lectura única al entrar: calcula los tres
    números en el constructor, los muestra, y ahí termina su trabajo. El
    dato no cambia mientras el usuario lo está mirando, porque no hay
    ninguna interacción en esta pantalla que lo cambie (no hay
    formulario, no hay búsqueda, nada que el usuario haga acá dispare un
    recálculo). Signals resuelve un problema real, mantener sincronizados
    la UI y los datos cuando algo cambia en vivo, como en la Sesión 3,
    pero ese problema cuesta algo: declarar los signals, pensar en
    efectos y dependencias. Pagar ese costo cuando no hay nada que
    sincronizar es complejidad de más. Saber cuándo no usar una
    herramienta es tan importante como saber usarla, y esta vista es el
    ejemplo concreto: la Sesión 3 te enseñó a construir con Signals, y
    esta te enseña a reconocer cuándo no hacen falta.

Corre la aplicación, entra a "Inicio", y confirma los tres números: el
valor total del inventario, la cantidad de productos, y cuántos están en
stock bajo.

---

## Parte 5: Comprobar que package-by-feature cumplió su promesa
Ya tienes las dos pantallas conectadas por un menú, y el dashboard
mostrando datos reales. Antes de cerrar, un ejercicio rápido: abre el
árbol de carpetas del proyecto y cuenta cuántos archivos dentro de
`product/` tuviste que tocar para agregar toda esta funcionalidad nueva,
el dashboard, sus KPIs, el menú, la navegación entre pantallas.

La respuesta es uno: `ProductListView.java`, y dentro de ese archivo, una
sola línea, el `@Route`. Todo lo demás (`DashboardView`, `MainLayout`,
`kpiCard`, el cálculo de los KPIs) es código nuevo, en paquetes nuevos,
que no tocó ni una entidad, ni un repositorio, ni un servicio de
`product/`.

![Navegación entre Inicio y Productos con el menú lateral](images/sesion7-navegacion.png)

!!! success "Lo que acabas de comprobar"
    Agregaste una funcionalidad completa (una vista nueva, cálculos
    propios, una entrada de menú) sin modificar ni una sola línea dentro
    de la lógica de negocio de `product/`. Todo lo nuevo vive en
    `dashboard/` y `shell/`. package-by-feature no es una preferencia
    estética: es la razón por la que esto fue una carpeta nueva y una
    línea de ruta, y no una cirugía sobre el código existente.

---

## Cierre de la sesión

```bash
git add .
git commit -m "sesion-7: navegación con App Shell y segunda feature (dashboard)"
git branch sesion-7
```

En la [Sesión 8](sesion_8.md), la última, le damos a StockPilot un tema
propio.
