# Cómo trabajamos en este repositorio

Adaptado de la forma de trabajo de *seguimiento-medicion*, en versión liviana para un proyecto personal.
La explicación de cada práctica está en [`docs/guia/01-metodologia.md`](docs/guia/01-metodologia.md).

## 1. Identificadores

| Prefijo | Qué es | Ejemplo |
|---|---|---|
| `RF-xx`, `RP-xx`, `RT-xx`, `RW-xx` | Requerimientos del producto ([`docs/requerimientos.md`](docs/requerimientos.md)) | RF-09 |
| `PA-xx` | Pruebas de aceptación del producto, a mano (sección 9 de los requerimientos) | PA-14 |
| `HU-xx` | Historia de usuario (un issue) | HU-02 |
| `CA-xx.n` | Criterio de aceptación *n* de la historia *xx* | CA-02.1 |
| `TEC-xx` | Ítem técnico (esqueleto, CI, instalador, spike) | TEC-01 |
| `DEF-x` | Defecto | DEF-3 |

## 2. Ramas

`main` está protegida: sólo se modifica mediante Pull Request.

| Tipo | Nombre de la rama |
|---|---|
| Historia de usuario | `feature/HU-02-crear-tarea` |
| Ítem técnico | `chore/TEC-02-esqueleto` |
| Prueba técnica (spike) | `spike/TEC-01-capa-del-fondo` |
| Defecto | `fix/DEF-3-descripcion-corta` |
| Documentación | `docs/cierre-hito-1` |

```bash
git switch main
git pull
git switch -c feature/HU-02-crear-tarea
```

## 3. Commits (evidencia de TDD)

[Conventional Commits](https://www.conventionalcommits.org/es/) con el ID de la historia y la fase de TDD:

```text
docs(HU-02): spec SDD y escenarios BDD
test(HU-02): RED - una tarea sin título no se crea
feat(HU-02): GREEN - crearTarea rechaza el título vacío
refactor(HU-02): extrae la validación del título
```

Un commit por fase: primero el test que falla (**RED**), después el código mínimo que lo hace pasar (**GREEN**)
y por último la mejora sin romper los tests (**REFACTOR**).

La parte de Windows (capa del fondo, bandeja, atajos) casi no se puede probar con tests automáticos. En esos
cambios, el PR lista las pruebas manuales hechas en la PC.

## 4. Pull Requests

1. `git push -u origin <rama>` y `gh pr create`.
2. Completar la plantilla del PR: trazabilidad, checklist y la sección «Qué se aprende acá».
3. Esperar a que el CI pase (cuando exista, desde TEC-03).
4. Mergear con **«Create a merge commit»** o **«Rebase and merge»**. *Squash* está deshabilitado porque borraría
   los commits RED/GREEN/REFACTOR.
5. Mover la tarjeta a **Hecho** en el tablero.

## 5. Hitos

Los hitos de [`docs/requerimientos.md`](docs/requerimientos.md) son milestones de GitHub: cada issue pertenece a uno.
Al cerrar un hito se escribe una nota corta en [`docs/hitos/`](docs/hitos/) con la plantilla
[`_plantilla-cierre.md`](docs/hitos/_plantilla-cierre.md).

## 6. Definition of Ready y Definition of Done

Ver [`docs/definition-of-ready-done.md`](docs/definition-of-ready-done.md).
