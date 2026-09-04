# StockPilot: inventario en Java puro con Vaadin Flow

Guía paso a paso (MkDocs Material) para construir un catálogo de inventario
con Vaadin Flow, Spring Boot y arquitectura package-by-feature, sin separar
el proyecto en frontend y backend.

## Ver la guía localmente

```bash
pip install mkdocs-material pymdown-extensions
mkdocs serve
```

Abre `http://localhost:8000`.

## Publicarla en GitHub Pages

El workflow en `.github/workflows/main.yml` ya está listo: cada push a
`main` publica el sitio con `mkdocs gh-deploy`. Solo necesitas:

1. Crear el repo en GitHub con el nombre que pusiste en `site_url` y
   `repo_url` dentro de `mkdocs.yml` (o editar esos dos valores si usás
   otro nombre).
2. Habilitar GitHub Pages para la rama `gh-pages` en la configuración del
   repo (Settings → Pages).
3. Hacer push a `main`.

## Estructura

```
docs/
  index.md          ← portada: el problema, por qué Vaadin, qué vamos a construir
  sesion_0.md ... sesion_8.md
  referencias.md
  images/           ← tus capturas de pantalla (ver docs/images/README.md)
mkdocs.yml
```

El proyecto de código en sí (el que vas a construir siguiendo esta guía)
es un repo Maven aparte: esta guía no lo incluye como código listo para
correr, la idea es que lo construyas tú mismo, sesión por sesión.
