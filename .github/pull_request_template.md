## Qué resuelve

Closes #<!-- número del issue -->

<!-- Resumen en 1 o 2 oraciones de lo que cambia. -->

## Trazabilidad

| Artefacto | Enlace |
|---|---|
| Requerimientos | RF-xx |
| Historia | HU-XX |
| Especificación SDD | `specs/HU-XX-....md` |
| Escenarios BDD | `features/hu-xx-....feature` |
| Tests | `...test.js` |

## Checklist (Definition of Done)

- [ ] La spec SDD está completa y actualizada.
- [ ] Los escenarios BDD cubren caso normal, alternativo, límite y error.
- [ ] Los tests se escribieron primero (hay commits `test(...): RED` antes de `feat(...): GREEN`).
- [ ] Los tests y el linter pasan localmente.
- [ ] Si toca la interfaz o Windows, hice las pruebas manuales de abajo en la PC.
- [ ] Si hay un concepto nuevo, lo explica la guía (`docs/guia/`).

## Cómo probarlo

<!-- Pasos para verificar el cambio, incluidas las pruebas manuales en la PC. -->

## Qué se aprende acá

<!-- Los conceptos nuevos que aparecen en este PR, explicados en pocas líneas. -->
