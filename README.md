# CRM interno en Java puro con Vaadin Flow

Guía (MkDocs Material) de un taller de 70 minutos: un CRM de clientes con
Grid, búsqueda reactiva con Signals, formulario validado con Binder, base
de datos real con Spring Data JPA, navegación y tema Aura propio. Todo en
Java, sin un frontend separado.

Stack: Java 25 · Spring Boot 4.1 · Vaadin 25.2 · H2.

Sigue el orden del tutorial de introducción a Vaadin Flow de Drew Harvey
([texto](https://drewharvey.github.io/vaadin-intro-tutorial/),
[código](https://github.com/drewharvey/vaadin-intro-tutorial)), adaptado
al español.

## Ver la guía localmente

```bash
pip install mkdocs-material pymdown-extensions
mkdocs serve
```

Abre `http://localhost:8000`.

## Publicarla en GitHub Pages

El workflow en `.github/workflows/main.yml` publica el sitio con
`mkdocs gh-deploy` en cada push a `main`. Solo necesitas:

1. Crear el repo en GitHub con el nombre que pusiste en `site_url` y
   `repo_url` dentro de `mkdocs.yml` (o editar esos dos valores).
2. Habilitar GitHub Pages para la rama `gh-pages` (Settings → Pages).
3. Hacer push a `main`.

## Estructura

```
docs/
  index.md          ← portada: el problema, la agenda de 70 min y las versiones
  sesion_0.md       ← preparación (antes del taller): proyecto y backend listos
  sesion_1.md ... sesion_8.md
  referencias.md
  images/           ← capturas por sesión (ver docs/images/README.md)
presentacion/
  StockPilot_Taller_Vaadin.pptx        ← diapositivas de apertura
  guion-facilitador-vaadin-completo.md ← guion en vivo, con tiempos por sesión
mkdocs.yml
```

El proyecto de código no está en este repo: cada asistente lo genera en
[start.vaadin.com](https://start.vaadin.com) y lo construye sesión por
sesión, con un commit al cierre de cada una.
