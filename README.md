# Corkanban

Un tablero de corcho con post-its que vive en el fondo de pantalla de Windows: tus tareas organizadas en un kanban,
siempre a la vista cuando mirás el escritorio.

> **Estado:** en desarrollo. Todavía no hay una versión para instalar.

## Qué hace

- Muestra un corcho como fondo de pantalla, detrás de todas las ventanas.
- Las tareas son post-its organizados en columnas (Pendiente, En espera, Hoy, En curso, Para entregar, Finalizado)
  y en filas por área (por ejemplo, Trabajo y Facultad).
- Se escribe directo sobre el post-it, se mueve arrastrando y lo finalizado queda en un registro para poder restaurarlo.
- Funciona sin internet: tus tareas se guardan solo en tu PC, en un archivo que elegís vos.

Los requerimientos completos están en [`docs/requerimientos.md`](docs/requerimientos.md).

## Stack

- **[Tauri 2](https://tauri.app/)**: arma la app de escritorio y el instalador.
- **HTML, CSS y JavaScript** sin frameworks: la interfaz y la lógica del tablero.
- **Rust**: la integración con Windows (ubicar el tablero como fondo, bandeja del sistema, atajos e inicio con Windows).
- **`node --test`** para TDD en JavaScript, **`cargo test`** en Rust y **cucumber-js** para los escenarios BDD en español.

## Metodología

Cada funcionalidad sigue la cadena de trazabilidad:

**Requerimiento → Historia de usuario → Especificación SDD → Criterios de aceptación → Escenarios BDD → Tests → Código**

| Artefacto | Dónde |
|---|---|
| Requerimientos del producto | [`docs/requerimientos.md`](docs/requerimientos.md) |
| Historias de usuario e ítems técnicos | Issues y tablero del proyecto |
| Especificaciones SDD | [`specs/`](specs/) |
| Escenarios BDD | [`features/`](features/) |
| Notas de cierre de cada hito | [`docs/hitos/`](docs/hitos/) |
| Guía para entender el proyecto | [`docs/guia/`](docs/guia/) |

Cómo trabajar en el repositorio (ramas, commits, Pull Requests): ver [CONTRIBUTING.md](CONTRIBUTING.md).

## Estructura

```text
docs/           requerimientos, Definition of Ready/Done, notas de hitos y guía
specs/          especificaciones SDD, una por historia
features/       escenarios BDD (.feature) en español
.github/        plantillas de issues y de Pull Request
```

La estructura del código se agrega con el esqueleto del proyecto, después de la prueba técnica del hito 0.

## Licencia

[MIT](LICENSE).
