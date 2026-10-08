# Instrucciones para Claude Code

- Proyecto personal de Mariano: Corkanban, un tablero corcho + kanban que funciona como fondo de pantalla de Windows.
  La fuente de verdad es `docs/requerimientos.md`; su sección 11 registra cada decisión tomada.
- Escribir en español rioplatense (voseo), en el chat, en los commits, en el código y en los documentos.
- Mariano quiere aprender: explicar cada paso en el chat, completar «Qué se aprende acá» en cada PR y agregar a
  `docs/guia/` los conceptos nuevos. Sabe Python sobre todo y algo de HTML, CSS, JavaScript, C y Java; no sabe Rust.
- Preguntar antes de tomar decisiones visibles para el usuario. Las menores se proponen como valor por defecto para
  que las apruebe.
- Seguir `CONTRIBUTING.md`: rama por issue, commits RED/GREEN/REFACTOR separados, PR con plantilla, merge sin squash.
- No agregar dependencias sin consultarlo. Aprobadas: `@tauri-apps/cli`, `@cucumber/cucumber` y `@biomejs/biome`
  (solo de desarrollo).
- Ante dos opciones de costo parecido, elegir la que facilite la futura versión para celular.
