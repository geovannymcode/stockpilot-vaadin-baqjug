# Referencias

## Vaadin Flow

- **Documentación general**: <https://vaadin.com/docs>
- **Signals (estado reactivo)**: <https://vaadin.com/docs/latest/flow/ui-state>  -  la guía oficial de cómo manejar estado de UI con Signals.
- **Effects y Signals computados**: <https://vaadin.com/docs/latest/flow/ui-state/effects-computed>
- **Component Bindings con Signals** (`bindValue`, `bindText`, `bindVisible`, `bindEnabled`): <https://vaadin.com/docs/latest/flow/ui-state/building-ui>
- **Binder** (validación y binding de formularios): <https://vaadin.com/docs/latest/flow/binding-data/binder>
- **Grid**: <https://vaadin.com/docs/latest/components/grid>
- **Theming y variantes**: <https://vaadin.com/docs/latest/styling>
- **Generador de temas** (para armar tu propia paleta de marca): busca "Vaadin theme builder" o "Vaadin theme editor" en <https://vaadin.com>

!!! note "Signals es una API relativamente nueva"
    Se volvió production-ready recién en Vaadin 25.1. Los nombres de clases
    y métodos de esta guía (`ValueSignal`, `Signal.computed`,
    `Signal.effect`, `bindValue`/`bindText`/`bindVisible`/`bindEnabled`)
    están confirmados contra la documentación oficial al momento de
    escribir esto. Si algo no compila tal cual en tu versión de Vaadin,
    revisa el changelog de Signals en la documentación  -  lo más probable es
    un cambio menor de nombre de método, no un cambio de concepto.

## Spring

- **Spring Boot**: <https://docs.spring.io/spring-boot/>
- **Spring Data JPA**: <https://docs.spring.io/spring-data/jpa/reference/>
- **Bean Validation** (la base de `asRequired`/`withValidator` en Binder): <https://docs.spring.io/spring-framework/reference/core/validation/beanvalidation.html>

## Organizar el código por funcionalidad (package-by-feature)

Package-by-feature (también llamado "package by component" o, en su
versión más completa, "vertical slice architecture") es un patrón de
organización de código discutido ampliamente en la comunidad de Spring y
Java como alternativa a la separación tradicional por capas técnicas
(`controller`/`service`/`repository`). Buscar "package by feature vs
package by layer" te va a llevar a varias charlas y artículos que
comparan ambos enfoques con más profundidad de la que cubre esta guía.

!!! note "Sobre cuándo usarla"
    Package-by-feature suma cuanto más funcionalidades de negocio
    independientes tenga el proyecto. Para un proyecto de una sola
    funcionalidad, la diferencia con package-by-layer es casi cosmética  - 
    el beneficio aparece cuando la segunda, tercera y cuarta funcionalidad
    entran en escena, como viste en la Sesión 7.

## El repo de esta guía

Si publicas tu propio avance como repo, este es un buen punto de partida
para el README:

- Cada sesión, una rama (`sesion-1` a `sesion-8`). `main` (o `sesion-8`) es
  la versión final, con tema propio.
- `docs/` es esta guía, pensada para servirse con MkDocs Material
  (`pip install mkdocs-material && mkdocs serve`).

---

¿Dudas o ideas? Escribime por [GitHub](https://github.com/geovannymcode) o
[LinkedIn](https://www.linkedin.com/in/geovannycode/).
