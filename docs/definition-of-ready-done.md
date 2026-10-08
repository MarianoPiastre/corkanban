# Definition of Ready y Definition of Done

## Definition of Ready: una historia se puede empezar si

- [ ] Está escrita en formato **Como / Quiero / Para**.
- [ ] Tiene **criterios de aceptación** (CA-xx.n).
- [ ] Indica qué **requerimientos** cubre (RF-xx, RP-xx…).
- [ ] Sus **dependencias** están identificadas y resueltas.
- [ ] Tiene **hito** (milestone) asignado.

## Definition of Done: una historia está terminada si

- [ ] La **especificación SDD** está completa en `specs/`.
- [ ] Los **escenarios BDD** (normal, alternativo, límite y error) están en `features/` y pasan.
- [ ] Los **tests** se escribieron con TDD: el historial muestra commits RED → GREEN → REFACTOR.
- [ ] La **cobertura** de la lógica del tablero es de al menos 80 % (meta propia; el CI la hace cumplir desde TEC-03).
- [ ] El formato y el linter no reportan problemas (Biome en JavaScript; `cargo fmt` y `clippy` en Rust).
- [ ] El **CI** pasa.
- [ ] Si toca la interfaz o la integración con Windows, se hicieron en esta PC las pruebas manuales que lista el PR.
- [ ] Si introduce un concepto nuevo, la **guía** (`docs/guia/`) lo explica.
- [ ] El PR se mergeó sin squash y el issue quedó cerrado.
