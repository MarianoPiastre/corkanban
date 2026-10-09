# Requerimientos de Corkanban: tablero corcho + kanban como fondo de pantalla

Versión 1.2 · 8 oct 2026 · @Mariano Piastrellini

Adaptado de «Requerimientos tablero corcho + kanban como fondo de Wallpaper Engine» (5 oct 2026), que se conserva sin cambios fuera de este repositorio. Todas las decisiones están tomadas y aprobadas (sección 11).

Cambios de la versión 1.1: la app se llama **Corkanban**, y las pruebas de la sección 9 pasan a llamarse PA-xx (pruebas de aceptación del producto), para no confundirlas con los criterios de aceptación de cada historia (CA-xx.n).

Cambios de la versión 1.2: los hitos pasan a ser rebanadas verticales con sus issues (sección 10), las versiones se publican en GitHub Releases y se registran las decisiones P-41 a P-45.

## 1. Resumen y objetivo

Se pide una aplicación de escritorio para Windows, **Corkanban**, que se muestre como fondo de pantalla: un corcho con post-its de tareas organizados en un kanban, con columnas por estado y filas por área (por defecto, Trabajo y Facultad).

**Por qué una app y no un fondo de Wallpaper Engine.** La prueba del hito 0 dentro de Wallpaper Engine (6 oct 2026) mostró que un fondo web no recibe doble clic, rueda del ratón ni teclado. Sin teclado no se puede escribir una tarea sobre el fondo, y las alternativas (panel de propiedades de WE, portapapeles) eran incómodas. Una aplicación propia controla sus propias entradas. La app reemplaza a Wallpaper Engine.

**Resultado esperado.** Al prender la PC, el escritorio muestra el tablero. Desde ahí se agregan, editan, mueven y finalizan tareas con ratón y teclado. Lo finalizado queda 24 horas a la vista y después pasa a un registro, donde se puede revisar y restaurar durante un plazo configurable (14 días por defecto).

**Alcance.** Uso personal, sin cuenta ni internet, con todo guardado en la propia PC. Se va a compartir con amigos para que la prueben, así que tiene que instalarse y configurarse en otra PC sin ayuda.

**A futuro (fuera de la v1).** Ver y modificar el mismo tablero desde el celular, sincronizado con la PC. No se diseña ahora, pero ante dos opciones de costo parecido se elige la que facilite ese camino.

**Criterios de éxito:**

- Se ve y se usa como fondo de pantalla: detrás de todas las ventanas y visible al mostrar el escritorio (Win+D o minimizar todo).
- Los datos sobreviven a cerrar la app, reiniciar, apagar la PC y también a un apagado forzado o un corte de luz (se pierde, como mucho, el último cambio).
- Se escribe con el teclado directamente sobre el post-it.
- Una tarea finalizada o eliminada se puede restaurar durante el plazo del registro.

## 2. Contexto de uso y restricciones del entorno

Lo que más condiciona el diseño es que Windows no ofrece una forma oficial de poner una aplicación interactiva como fondo de pantalla.

| Restricción | Qué se sabe | Consecuencia para el diseño |
| --- | --- | --- |
| Sistema operativo | Windows 11 Home (compilación 26300), un monitor de 1920×1080. Los amigos pueden tener Windows 10 y otras pantallas. | Se desarrolla y prueba en Windows 11. Windows 10 se tiene en cuenta en cada decisión, sin probarlo. |
| Capa del fondo | Windows no tiene una API pública para fondos interactivos. Wallpaper Engine y Lively Wallpaper ubican su ventana en la capa del escritorio, detrás de los íconos, con una técnica no documentada que cambió en versiones recientes de Windows 11. | Es el mayor riesgo técnico. Se valida primero en esta PC (hito 0 nuevo, sección 10). |
| Teclado | En WE el teclado no llegó al fondo. Una ventana ubicada en la capa del escritorio probablemente tampoco reciba el teclado de forma normal. | La edición sobre el post-it puede necesitar una ventanita superpuesta, invisible para el usuario, que sí reciba el teclado (incluidas tildes, ñ y mayúsculas). |
| Ratón | La capa de íconos del escritorio queda por encima del fondo y recibe el ratón primero. | La app tiene que recibir clic, doble clic, arrastre y rueda aunque esa capa esté encima. |
| Wallpaper Engine | Ocupa la misma capa y hoy arranca con Windows. | La app lo reemplaza. El README explica cómo desactivar su inicio con Windows. |
| Íconos del escritorio | Se usan. Si se muestran, quedan dibujados encima del fondo. | Se reserva una franja libre a la izquierda, de ancho configurable. |
| Barra de tareas | Ocupa la parte inferior (48 px a escala 100 % en Windows 11). | El tablero usa el área que deja libre la barra, sin superponerse. |
| Conexión | Debe funcionar sin internet. | La app nunca se conecta a internet. Sin CDN ni fuentes web. Las actualizaciones se instalan a mano. |
| Arranque | El tablero tiene que estar al prender la PC. | El instalador activa el inicio con Windows. |
| Recursos | Corre todo el día, también mientras se juega o se trabaja. | Sin consumo apreciable en reposo y en pausa con aplicaciones a pantalla completa. |

**Momento de uso.** Se interactúa con el tablero cuando el escritorio está a la vista. Con ventanas abiertas, un atajo global permite anotar una tarea rápida, y el ícono de la bandeja da acceso a lo demás.

**Lo que se aprendió del fondo de WE (hito 0, 6 oct 2026)**

- Funcionaron: clic, paso del ratón, arrastre con Pointer Events, guardado local tras reiniciar WE, descargas y portapapeles.
- No funcionaron: doble clic, rueda del ratón y teclado.
- Se mantiene de aquel diseño: el aspecto de corcho, el kanban, el arrastre propio y las protecciones contra borrados accidentales.
- Queda obsoleto: el panel de propiedades de WE como vía de entrada, `project.json` y la carpeta `hito0`.

## 3. Requerimientos funcionales

El tablero es una matriz: las columnas son estados y las filas son áreas. Prioridad según MoSCoW (Must = imprescindible, Should = importante, Could = deseable).

**Filas y columnas**

| ID | Requerimiento | Prioridad |
| --- | --- | --- |
| RF-01 | Filas visibles a la vez (sin pestañas), cada una con su color y su rótulo vertical a la izquierda. En esta PC: Trabajo y Facultad. En una instalación nueva las define el asistente inicial (RF-33). | Must |
| RF-02 | Columnas iniciales, compartidas por todas las filas, en este orden: Pendiente · En espera · Hoy · En curso · Para entregar · Finalizado. | Must |
| RF-03 | Agregar columnas a demanda. Una columna nueva aparece en todas las filas. | Must |
| RF-04 | Renombrar y reordenar columnas. Eliminar una columna solo se permite cuando está vacía en todas las filas. Pendiente y Finalizado no se pueden eliminar. | Must |
| RF-05 | Agregar, renombrar, reordenar y eliminar filas. Eliminar una fila también exige que esté vacía. | Should |
| RF-06 | Colapsar una fila: queda una banda angosta con su nombre y su contador, y las demás filas ocupan el alto libre. | Should |
| RF-07 | Contador de tareas no finalizadas en el rótulo de cada fila. | Should |
| RF-08 | Celda llena: cuando los post-its no entran en una celda, esa celda pasa a modo compacto (solo el título, como tiras) y cada tira se amplía al pasar el ratón. | Must |

**Tareas (post-its)**

| ID | Requerimiento | Prioridad |
| --- | --- | --- |
| RF-09 | Crear con un botón «+» en la celda Pendiente de cada fila. La tarea nueva entra al final de Pendiente de esa fila. | Must |
| RF-10 | Campo obligatorio: título (hasta 140 caracteres). | Must |
| RF-11 | Campos opcionales: detalle (hasta 500 caracteres), fecha límite (solo fecha), prioridad (alta, media o baja), checklist, etiquetas, motivo de espera y enlaces. | Must |
| RF-12 | Un campo vacío no se muestra de ninguna forma. Por ejemplo, un post-it sin fecha límite no muestra «Sin fecha»: simplemente no tiene esa línea. | Must |
| RF-13 | Antigüedad: el post-it muestra cuánto tiempo lleva en su columna actual (por ejemplo, «hace 5 días en En curso»). Se reinicia al cambiar de columna. | Should |
| RF-14 | Color del post-it según la prioridad. Los colores se configuran en los ajustes. Por defecto: alta rosa, media naranja, baja verde y sin prioridad amarillo. | Must |
| RF-15 | Checklist: ítems tildables. Fuera de la edición, el post-it muestra solo el progreso (por ejemplo, «☑ 2/5»). | Should |
| RF-16 | Etiquetas libres: se escribe cualquiera y se sugieren las ya usadas. Todas con el mismo estilo. | Should |
| RF-17 | Motivo de espera: al soltar una tarea en En espera se pide qué o a quién se espera y, opcionalmente, hasta cuándo. Se puede saltear. | Should |
| RF-18 | Enlaces a archivos, carpetas o páginas web, que se abren con un clic. | Could |
| RF-19 | Editar: un clic sobre el post-it lo agranda en el lugar y muestra todos sus campos editables. Mantener apretado y mover es arrastrar, no editar. | Must |
| RF-20 | Mover una tarea entre columnas y entre filas arrastrándola, y reordenarla dentro de su celda. El orden es manual. | Must |
| RF-21 | Alternativa al arrastre: botones ◀ ▶ al pasar el ratón, para moverla a la columna vecina con un clic. | Should |
| RF-22 | Fecha límite fuera de Finalizado: en rojo si ya venció y en naranja si vence hoy o mañana, siempre con texto además del color. | Should |
| RF-23 | Columna Hoy: a la medianoche (o al prender la PC al día siguiente) las tareas que siguen en Hoy vuelven al final de Pendiente de su fila. | Should |
| RF-24 | Eliminar una tarea la manda al registro marcada como «eliminada», recuperable durante el mismo plazo que las finalizadas. | Must |
| RF-25 | Deshacer la última acción (mover, finalizar, eliminar, editar) con Ctrl+Z y con un botón en el aviso que aparece tras la acción. | Should |
| RF-26 | Buscar y filtrar por texto y por etiqueta en todas las filas. Las tareas que no coinciden se atenúan. | Should |

**Finalización y registro**

| ID | Requerimiento | Prioridad |
| --- | --- | --- |
| RF-27 | Al soltar una tarea en Finalizado se guardan la fecha y hora de finalización y la columna de la que venía. | Must |
| RF-28 | La tarea finalizada queda atenuada en Finalizado durante 24 horas y después deja de verse en el tablero. Si en ese lapso se la saca de Finalizado, se borra su fecha de finalización. | Must |
| RF-29 | Registro: lista de tareas finalizadas y eliminadas, agrupada por día y filtrable por fila, con título, fila, columna de origen, fecha y motivo (finalizada o eliminada). | Must |
| RF-30 | Restaurar desde el registro: se pregunta a qué columna vuelve (sugiriendo la de origen), en su misma fila. | Must |
| RF-31 | Plazo del registro configurable en los ajustes, 14 días por defecto. Pasado el plazo, la tarea se borra definitivamente. | Must |
| RF-32 | Los plazos (24 horas en Finalizado, medianoche de Hoy, vencimiento del registro) se revisan al iniciar la app, al volver de la suspensión y en el momento exacto en que vence el próximo, sin revisar en bucle. | Must |

**Instalación en otra PC**

| ID | Requerimiento | Prioridad |
| --- | --- | --- |
| RF-33 | Asistente del primer inicio: pide la carpeta de datos y los nombres de las filas (sugiere Trabajo y Facultad). Todo se puede cambiar después en los ajustes. | Must |
| RF-34 | Elegir en los ajustes en qué monitor aparece el tablero (por defecto, el principal). En los demás se ve el fondo normal de Windows. | Should |

**Apariencia**

| ID | Requerimiento | Prioridad |
| --- | --- | --- |
| RF-35 | Tema claro y tema oscuro del corcho, que se eligen a mano en los ajustes. | Could |

**Estados vacíos y errores**

Una celda vacía muestra un texto breve (por ejemplo, «Sin tareas»). Un error de guardado o de importación se avisa de forma discreta, sin tapar el tablero.

## 4. Interacción y entrada de datos

**Ratón**

- Clic, doble clic, arrastre y rueda.
- Arrastre propio, con la celda de destino resaltada, el post-it levantado con más sombra y cursor de arrastre.
- Zonas de clic de al menos 32 px de alto.

**Teclado en la edición sobre el post-it**

- En el título, Enter guarda. En el detalle, Enter es salto de línea y Ctrl+Enter guarda.
- Hacer clic afuera también guarda. Esc descarta los cambios de esa edición.
- Al crear con «+», aparece un post-it vacío con el cursor en el título. Si se sale sin escribir título, no se crea nada.

**Teclado en el tablero**

- Supr elimina la tarea seleccionada (va al registro) y Ctrl+Z deshace.

**Accesos con otras ventanas abiertas**

- Atajo global de tarea rápida (por defecto Ctrl+Alt+N, configurable): abre una ventanita con el título y la fila (por defecto, la última usada). El resto de los campos se completa después en el tablero.
- Ícono en la bandeja del sistema con un menú: nueva tarea, ver registro, ajustes y salir.

**Prevención de errores**

- Nada se pierde al primer clic: eliminar manda al registro y toda acción se puede deshacer.
- Las acciones destructivas (eliminar una fila o columna, borrar algo del registro de forma definitiva) van separadas de las de uso frecuente y piden confirmación.

## 5. Persistencia, respaldo y estructura de datos

Perder tareas es el peor fallo posible, así que el guardado se diseña para resistir apagados y cortes de luz.

| ID | Requerimiento | Prioridad |
| --- | --- | --- |
| RP-01 | Guardado automático tras cada cambio, agrupando cambios seguidos (unos 300 ms). | Must |
| RP-02 | Datos en un archivo JSON dentro de la carpeta elegida en el asistente inicial, cambiable en los ajustes (al cambiarla, los datos se mueven). | Must |
| RP-03 | Escritura segura: se escribe primero un archivo temporal y después reemplaza al original, para que un corte de luz a mitad del guardado no deje el archivo dañado. | Must |
| RP-04 | Si el archivo no se puede leer o está dañado, no se sobrescribe: se aparta con otro nombre, se avisa y se ofrece restaurar desde el respaldo. | Must |
| RP-05 | Respaldo automático diario en la misma carpeta, que sobrescribe siempre el mismo archivo (decisión del 6 oct 2026). | Should |
| RP-06 | Exportar todo a un archivo JSON e importarlo, validando la estructura y pidiendo confirmación antes de reemplazar los datos. | Must |
| RP-07 | Campo `schemaVersion` y función de migración para cambios futuros. | Should |
| RP-08 | Verificar que los datos sobreviven a cerrar la app, reiniciar, apagar y forzar un apagado. | Must |
| RP-09 | Pensando en la sincronización futura: identificadores únicos globales (UUID) y fecha de última modificación en cada fila, columna y tarea. | Should |
| RP-10 | Los ajustes propios de cada PC (carpeta de datos, monitor, atajo, ancho de la franja de íconos, inicio con Windows) se guardan aparte de los datos del tablero, en la configuración local de la app. | Should |
| RP-11 | Desinstalar la app no borra la carpeta de datos. | Must |

**Estructura de datos (preliminar)**

Identificadores únicos globales, fechas en formato ISO con zona horaria y orden como entero dentro de su celda. Las columnas con comportamiento propio se reconocen por su `role`, no por su nombre, así que se pueden renombrar.

```json
{
  "schemaVersion": 1,
  "settings": {
    "retentionDays": 14,
    "theme": "claro",
    "priorityColors": { "alta": "#f8b4c4", "media": "#ffc98b", "baja": "#b8e6b0", "ninguna": "#ffe66d" }
  },
  "rows": [
    { "id": "uuid-fila-1", "name": "Trabajo", "color": "#c0392b", "order": 0, "collapsed": false, "updatedAt": "2026-10-07T10:00:00-03:00" },
    { "id": "uuid-fila-2", "name": "Facultad", "color": "#2471a3", "order": 1, "collapsed": false, "updatedAt": "2026-10-07T10:00:00-03:00" }
  ],
  "columns": [
    { "id": "uuid-col-1", "name": "Pendiente", "order": 0, "role": "entrada" },
    { "id": "uuid-col-2", "name": "En espera", "order": 1, "role": "espera" },
    { "id": "uuid-col-3", "name": "Hoy", "order": 2, "role": "hoy" },
    { "id": "uuid-col-4", "name": "En curso", "order": 3, "role": null },
    { "id": "uuid-col-5", "name": "Para entregar", "order": 4, "role": null },
    { "id": "uuid-col-6", "name": "Finalizado", "order": 5, "role": "salida" }
  ],
  "notes": [
    {
      "id": "uuid-tarea-1",
      "rowId": "uuid-fila-2",
      "columnId": "uuid-col-2",
      "order": 0,
      "title": "Entregar TP 2",
      "detail": "Falta la conclusión",
      "dueDate": "2026-10-12",
      "priority": "alta",
      "checklist": [{ "id": "uuid-item-1", "text": "Conclusión", "done": false }],
      "tags": ["Física II"],
      "waiting": { "what": "Corrección del profe", "until": "2026-10-10" },
      "links": [{ "label": "Carpeta del TP", "target": "C:\\Users\\maria\\Documents\\TP2" }],
      "createdAt": "2026-10-07T10:00:00-03:00",
      "columnEnteredAt": "2026-10-07T10:00:00-03:00",
      "finishedAt": null,
      "updatedAt": "2026-10-07T10:00:00-03:00"
    }
  ],
  "archive": [
    {
      "note": { "id": "uuid-tarea-0", "rowId": "uuid-fila-1", "title": "Enviar informe semanal" },
      "fromColumnId": "uuid-col-4",
      "reason": "finalizada",
      "at": "2026-10-06T18:30:00-03:00"
    }
  ]
}
```

Los campos opcionales vacíos no se guardan, en línea con RF-12.

## 6. Diseño visual

Se mantiene el aspecto del documento original: un corcho con post-its reales, donde la legibilidad pesa más que el realismo.

**Fondo de corcho**

- Textura generada por código, o una imagen local de menos de unos 300 KB.
- Tono marrón cálido, con un oscurecimiento suave en los bordes. Versión oscura para el tema oscuro.

**Post-its**

- Casi cuadrados, con una rotación leve (entre -2° y 2°) calculada a partir del id, para que no cambie en cada dibujado.
- Sombra suave y chincheta de color en el borde superior.
- Color según la prioridad (RF-14).
- Tipografía manuscrita del sistema: `"Segoe Print", "Comic Sans MS", cursive`.
- Título de al menos 16 px en 1080p. Detalle, fechas, antigüedad y etiquetas en tamaño menor.
- Solo se muestran los campos que tienen contenido (RF-12).
- Al editar, el post-it se agranda sobre los vecinos sin moverlos.

**Filas y columnas**

- Columnas: franjas semitransparentes que atraviesan todas las filas, con una cinta de papel como título.
- Filas: bandas horizontales separadas por una línea suave (por ejemplo, un hilo), con el rótulo vertical a la izquierda, en el color de la fila y con su contador.
- Finalizado: post-its atenuados durante sus 24 horas, y en el encabezado el acceso al registro.
- Registro: panel lateral sobre el corcho, con la misma estética.
- Búsqueda: un campo discreto en el borde superior.

**Escala y márgenes**

- Se adapta a la escala de Windows (100 %, 125 %…) y al área que deja libre la barra de tareas.
- Franja libre a la izquierda para los íconos del escritorio, de ancho configurable.

**Movimiento**

- Animaciones de 200 ms o menos, solo al mover, soltar o agrandar un post-it. Nada animado en reposo.

**Accesibilidad**

- Contraste mínimo de 4.5:1 entre el texto y el post-it, en los dos temas y con cualquier color de prioridad.
- La prioridad y el vencimiento llevan texto o ícono, no solo color.

## 7. Requerimientos técnicos y de rendimiento

| ID | Requerimiento | Detalle |
| --- | --- | --- |
| RT-01 | Tecnología | Tauri 2 (**P-23**), con la interfaz en HTML, CSS y JavaScript sin frameworks y la integración con Windows en Rust. Si el hito 0 falla por algo propio de Tauri, se pasa a C# (.NET) con la misma interfaz web. |
| RT-02 | Dependencias | Ninguna que requiera internet durante el uso. Cualquier librería se consulta antes de agregarla. |
| RT-03 | Estado | Una única fuente de verdad (sección 5). Las operaciones (crear, mover, finalizar, restaurar) son funciones que reciben el estado y devuelven el nuevo, para poder probarlas y reutilizarlas a futuro en el celular. |
| RT-04 | Separación | La lógica del tablero, separada de la integración con Windows (capa del fondo, bandeja, atajos, inicio), para poder desarrollar y probar el tablero en una ventana común. |
| RT-05 | Dibujado | Se redibuja solo cuando cambia el estado. Sin bucles en reposo: como mucho, un temporizador programado para el próximo plazo (RF-32). |
| RT-06 | Rendimiento | Fluido con 200 post-its. Respuesta al arrastrar menor a 100 ms. |
| RT-07 | Consumo | CPU cercana a 0 % en reposo. Con una app a pantalla completa (juego, video) el tablero no dibuja. Memoria lo más baja que permita la tecnología elegida. |
| RT-08 | Instancia única | Abrir la app dos veces no crea un segundo tablero ni compite por el archivo de datos. |
| RT-09 | Robustez | Si Windows reinicia el escritorio (explorer.exe), al volver de la suspensión o al cambiar la resolución o los monitores, el tablero reaparece solo y bien ubicado. Si la app se cierra, se ve el fondo normal de Windows. |
| RT-10 | Seguridad básica | El texto del usuario nunca se interpreta como código (`textContent`, nunca `innerHTML`). El JSON importado se valida. Sin `eval`. Los enlaces se abren con el programa predeterminado de Windows y nunca se ejecutan dentro de la app. |
| RT-11 | Idioma | Interfaz en español rioplatense, con los textos en un solo lugar. |
| RT-12 | Resolución | Pensado para 1920×1080 y legible también en 1366×768. Sin tamaños fijos para el contenedor principal. |
| RT-13 | Windows 10 | Ninguna decisión debe impedir que funcione en Windows 10 actualizado. No se prueba durante el desarrollo. |
| RT-14 | Código | Comentarios breves en español, nombres claros y sin código duplicado. |

## 8. Integración con Windows

Esta sección reemplaza a «Integración con Wallpaper Engine».

| ID | Requerimiento | Prioridad |
| --- | --- | --- |
| RW-01 | El tablero vive en la capa del fondo: detrás de todas las ventanas y visible con Win+D o al minimizar todo. Nunca queda por encima de otra ventana. | Must |
| RW-02 | La app no aparece en la barra de tareas ni en Alt+Tab. Se maneja desde el tablero y desde el ícono de la bandeja. | Should |
| RW-03 | Inicio automático con Windows, activado por el instalador y desactivable en los ajustes. | Must |
| RW-04 | Franja libre para los íconos del escritorio, a la izquierda, de ancho configurable. | Must |
| RW-05 | Ícono en la bandeja del sistema (sección 4). | Should |
| RW-06 | Atajo global de tarea rápida (sección 4). | Should |
| RW-07 | Ajustes accesibles desde un engranaje discreto en el tablero y desde la bandeja: carpeta de datos, filas, monitor, franja de íconos, atajo, colores de prioridad, tema, plazo del registro, inicio con Windows, exportar e importar. | Should |

**Entregables**

- Un instalador (`setup.exe`) que instala la app, crea el acceso directo, activa el inicio con Windows y permite desinstalarla sin borrar los datos.
- Las versiones nuevas se entregan como un instalador nuevo que se instala encima y conserva los datos. Cada versión se publica en GitHub Releases, desde donde se descarga el instalador.
- Un `README.md` con la instalación (incluido el aviso de Windows SmartScreen, ver sección 10), cómo desactivar el inicio de Wallpaper Engine y dónde quedan los datos y el respaldo.

## 9. Pruebas de aceptación del producto y plan de pruebas

El proyecto se considera terminado cuando pasan todas las pruebas Must en esta PC, con la app funcionando como fondo y no solo en una ventana. Son pruebas del producto completo, que se hacen a mano; cada historia tiene además sus propios criterios de aceptación (CA-xx.n), que se automatizan con BDD.

| ID | Prueba | Resultado esperado | Prioridad |
| --- | --- | --- | --- |
| PA-01 | Crear una tarea con el «+» de cada fila. | Aparece al final de Pendiente de esa fila, y solo ahí. | Must |
| PA-02 | Crear una tarea solo con título. | El post-it muestra el título y nada más (ni «sin fecha» ni campos vacíos). | Must |
| PA-03 | Editar una tarea con un clic, escribiendo tildes, ñ y mayúsculas. | Se escribe sin abrir otro programa y se guarda al hacer clic afuera. | Must |
| PA-04 | Arrastrar una tarea por todas las columnas hasta Finalizado. | Se mueve en orden y al soltarla en En espera se pide el motivo. En Finalizado queda atenuada. | Must |
| PA-05 | Simular que pasaron 24 horas desde la finalización. | La tarea deja de verse en el tablero y está en el registro. | Must |
| PA-06 | Restaurar esa tarea desde el registro. | Se pregunta la columna y vuelve a su fila, sin fecha de finalización. | Must |
| PA-07 | Simular que pasaron más días que el plazo del registro. | La tarea desaparece del registro y de los datos. | Must |
| PA-08 | Eliminar una tarea por error. | Se puede deshacer al instante y también restaurar desde el registro. | Must |
| PA-09 | Dejar una tarea en Hoy y simular el cambio de día. | Vuelve al final de Pendiente de su fila. | Should |
| PA-10 | Llenar una celda con más post-its de los que entran. | Pasa a modo compacto y cada tira se amplía al pasar el ratón. | Must |
| PA-11 | Agregar una columna. Intentar eliminar una columna con tareas. | La nueva aparece en todas las filas. La que tiene tareas no se puede eliminar. | Must |
| PA-12 | Cerrar y abrir la app; reiniciar; apagar y prender. | Filas, columnas, tareas y registro siguen igual. | Must |
| PA-13 | Forzar un apagado justo después de un cambio. | Al prender, los datos están completos y sanos (como mucho falta el último cambio). | Must |
| PA-14 | Win+D, minimizar todo y abrir y cerrar ventanas encima. | El tablero se ve, responde y nunca tapa otra ventana. | Must |
| PA-15 | Con una ventana abierta, usar el atajo de tarea rápida. | Se anota la tarea y aparece en Pendiente de la fila elegida. | Should |
| PA-16 | Exportar, borrar todo e importar. Importar un archivo inválido. | El estado queda idéntico. El inválido se rechaza sin tocar los datos. | Must |
| PA-17 | Reiniciar explorer.exe y suspender y reanudar la PC. | El tablero reaparece solo. | Must |
| PA-18 | Prender la PC sin tocar nada. | El tablero aparece como fondo. | Must |
| PA-19 | Desconectar internet y reiniciar. | Funciona igual. | Must |
| PA-20 | Abrir en 1920×1080 y en 1366×768 con la barra de tareas visible. | Todo legible, sin cortes ni superposiciones con la barra ni con la franja de íconos. | Must |
| PA-21 | Instalar en limpio (o en otro usuario de Windows). | El asistente pide carpeta y filas, y el tablero arranca con ellas. | Must |
| PA-22 | Desinstalar y volver a instalar. | Los datos siguen en su carpeta y la app los vuelve a usar. | Must |
| PA-23 | Buscar un texto y una etiqueta. | Se atenúan las tareas que no coinciden, en todas las filas. | Should |
| PA-24 | Cargar 200 tareas de prueba y arrastrar una. | Respuesta fluida. | Should |
| PA-25 | Dejar el tablero 10 minutos sin tocar, mirando el Administrador de tareas. | CPU cercana a 0 %. | Should |
| PA-26 | Vencer una fecha límite fuera de Finalizado; poner otra para mañana. | La vencida se ve en rojo y la de mañana en naranja, con texto. | Should |

**Cómo informar los resultados.** Al final de cada hito, Claude Code lista qué pruebas pasaron, cuáles no pudo probar y qué quedó como supuesto. Las pruebas que dependen de otra PC (Windows 10, varios monitores) quedan como supuesto.

## 10. Plan de implementación, decisiones asumidas y fuera de alcance

Lo primero es validar la capa del fondo en esta PC, porque de eso depende toda la integración. Después, cada hito es una **rebanada vertical**: suma lógica, interfaz y guardado a la vez, y deja una versión usable. Desde el hito 1 el tablero ya se puede usar todos los días como fondo.

**Hitos**

Cada hito es un milestone de GitHub y sus historias son issues (P-43).

| Hito | Objetivo | Issues |
| --- | --- | --- |
| 0 · Spike: la capa del fondo | Comprobar en esta PC que una ventana de Tauri se puede usar como fondo interactivo: detrás de las ventanas, visible con Win+D, con ratón y teclado (tildes y ñ), y que reaparece tras reiniciar explorer.exe y tras suspender. Si algo falla por la tecnología, se propone cambiarla antes de seguir. | TEC-01 |
| 1 · MVP: un corcho usable en el escritorio | Ver, crear, editar el título, mover y guardar tareas, con el tablero como fondo y arranque automático. | TEC-02, TEC-03, HU-01 a HU-08 |
| 2 · Terminar y recuperar | Finalizar, registro, restaurar, eliminar, deshacer y vencimiento del registro. | HU-09 a HU-14 |
| 3 · Post-its completos | Todos los campos de la tarea, modo compacto y botones ◀ ▶. | HU-15 a HU-22 |
| 4 · Organizar el tablero | Columna Hoy, columnas y filas gestionables, filas colapsables, búsqueda. | HU-23 a HU-27 |
| 5 · Accesos rápidos y datos seguros | Bandeja, atajo de tarea rápida, respaldo, exportar e importar, pausa con pantalla completa. | HU-28 a HU-32 |
| 6 · Listo para compartir | Asistente inicial, ajustes, monitor, tema oscuro e instalador. | HU-33 a HU-36, TEC-04 |
| 7 · Cierre de la v1 | Pruebas PA-01 a PA-26, pulido y publicación en GitHub Releases. | TEC-05, TEC-06 |

Hasta que exista el asistente inicial (HU-33), los datos se guardan en `Documentos\Corkanban`, y los ajustes de cada PC (franja de íconos, inicio con Windows) se cambian en el archivo de configuración local.

**Cómo debe trabajar Claude Code**

- Seguir [`CONTRIBUTING.md`](../CONTRIBUTING.md): rama por issue, commits RED → GREEN → REFACTOR y PR con plantilla.
- Entregar cada hito funcionando antes de empezar el siguiente.
- No agregar dependencias sin consultarlo.
- Si el hito 0 contradice alguna suposición de este documento, proponer el cambio en lugar de forzar la solución original.
- Ante dos opciones de costo parecido, elegir la que facilite la futura versión para celular.

**Decisiones asumidas (P-27, aprobadas el 8 oct 2026)**

1. Pendiente es siempre la primera columna y Finalizado la última. Ninguna de las dos se puede eliminar, pero sí renombrar.
2. Hoy y En espera se pueden renombrar, mover o eliminar. Su comportamiento especial (vaciarse a la medianoche, pedir el motivo) va con la columna aunque cambie de nombre.
3. Máximo de 8 columnas (las 6 iniciales y 2 más), por el ancho de la pantalla.
4. Las tareas se pueden mover entre filas arrastrándolas.
5. Eliminar una tarea no pide confirmación, porque va al registro y se puede deshacer.
6. El plazo del registro se cuenta desde que la tarea se finalizó o eliminó. El registro también muestra las finalizadas que todavía se ven en Finalizado.
7. Desde el registro se puede borrar una tarea de forma definitiva antes del plazo, con confirmación.
8. La antigüedad se muestra a partir del primer día completo en la columna.
9. Al cumplirse la fecha de «hasta cuándo» del motivo de espera, el post-it se destaca igual que una fecha límite.
10. Varios enlaces por tarea. En el post-it se ve un ícono con la cantidad.
11. Respaldo diario que sobrescribe siempre el mismo archivo, más exportar e importar a mano.
12. La carpeta de datos se elige en el asistente del primer inicio, sugiriendo `Documentos\Corkanban` (antes `Documentos\Corcho`; cambió con el nombre de la app).
13. El instalador no lleva firma digital, porque un certificado de firma es pago. Al instalar, Windows SmartScreen va a mostrar «Windows protegió tu PC» y hay que tocar «Más información» y «Ejecutar de todas formas».
14. Interfaz en HTML, CSS y JavaScript sin frameworks ni compilación de la parte web, para que la puedas leer y tocar.
15. Aspecto visual del documento original (corcho, chincheta, letra manuscrita).

**Fuera de alcance de la v1**

- Versión para celular y sincronización (se facilita a futuro con RP-09, RP-10 y RT-03).
- Avisos y notificaciones de vencimiento.
- Tareas repetitivas.
- Límite de tareas en En curso, resumen semanal y reloj en el corcho.
- Actualización automática.
- Adjuntar archivos (solo enlaces).
- Colaboración con otras personas.

## 11. Registro de decisiones

| ID | Pregunta | Respuesta (7 oct 2026) |
| --- | --- | --- |
| P-01 | ¿La app reemplaza a Wallpaper Engine? | Sí, lo reemplaza. |
| P-02 | ¿Qué se hace con los íconos del escritorio? | Se usan. Se reserva una franja libre de ancho configurable. |
| P-03 | ¿Cómo se escribe y edita? | Todo sobre el post-it, que se agranda en el lugar. |
| P-04 | Accesos con ventanas abiertas | Atajo global de tarea rápida e ícono en la bandeja. |
| P-05 | Columnas extra | Hoy y Para entregar. |
| P-06 | Filas | Gestionables (agregar, renombrar, reordenar, eliminar) y colapsables. |
| P-07 | Eliminar una columna con tareas | No se permite hasta que esté vacía. |
| P-08 | Celda llena | Modo compacto: solo título, se amplía al pasar el ratón. |
| P-09 | Campos básicos | Detalle, fecha límite, prioridad y antigüedad visible. Un campo vacío no se muestra. |
| P-10 | Campos extra | Checklist, etiquetas, motivo de espera y enlaces. |
| P-11 | Color del post-it | Según la prioridad. |
| P-12 | Crear una tarea | Un «+» por fila, en la celda Pendiente. |
| P-13 | Orden en la celda | Manual. Las nuevas van al final. |
| P-14 | Fecha límite | Solo fecha. Rojo si venció, naranja si vence hoy o mañana. |
| P-15 | Eliminar | Va al registro como «eliminada». |
| P-16 | Cuándo sale de Finalizado | A las 24 horas. |
| P-17 | Plazo del registro | Configurable, 14 días por defecto. |
| P-18 | Al vencer el plazo | Se borra definitivamente. |
| P-19 | Restaurar | Se pregunta la columna. |
| P-20 | Ubicación de los datos | Se elige al instalar (asistente del primer inicio) y se puede cambiar. |
| P-21 | Cuántas PCs | Por ahora esta. Se compartirá con amigos. A futuro, celular sincronizado: preferir opciones que lo faciliten sin aumentar el costo. |
| P-22 | Conocimientos | Python sobre todo; algo de HTML, CSS, JavaScript, C y Java. |
| P-23 | Tecnología | Que la proponga Claude. Propuesta aprobada el 8 oct 2026: Tauri 2, con C# (.NET) como alternativa si el hito 0 falla. |
| P-24 | Entrega | Instalador. |
| P-25 | Extras (1) | Buscar y filtrar. |
| P-26 | Extras (2) | Tema oscuro. |
| P-27 | Decisiones asumidas | Aprobadas las 15 el 8 oct 2026. |
| P-28 | Orden de columnas | Pendiente · En espera · Hoy · En curso · Para entregar · Finalizado. |
| P-29 | Colores por prioridad | Configurables. Por defecto: alta rosa, media naranja, baja verde, sin prioridad amarillo. |
| P-30 | Qué antigüedad se muestra | El tiempo en la columna actual. |
| P-31 | Etiquetas | Libres, con autocompletar, todas del mismo estilo. |
| P-32 | Checklist fuera de la edición | Solo el progreso. |
| P-33 | Cómo se entra a editar | Clic simple. |
| P-34 | Motivo de espera | Se pregunta al soltar en En espera. Se puede saltear. |
| P-35 | Hoy a la medianoche | Las tareas vuelven a Pendiente. |
| P-36 | Primer inicio en otra PC | Asistente: carpeta de datos y filas. |
| P-37 | Windows 10 | Tenerlo en cuenta en las decisiones, sin probarlo. |
| P-38 | Varios monitores | Elegible en los ajustes. Por defecto, el principal. |
| P-39 | Actualizaciones | Instalador nuevo a mano. La app nunca se conecta a internet. El 8 oct se sumó publicar cada versión en GitHub Releases (P-44). |
| P-40 | Tema oscuro | Se elige a mano en los ajustes. |
| P-41 | Nombre | Corkanban (cork + kanban). Repositorio `corkanban`. |
| P-42 | Repositorio | Público en GitHub, con licencia MIT, en `Documents\GitHub\corkanban`. |
| P-43 | Hitos | Rebanadas verticales, con un MVP usable como fondo al final del hito 1 (sección 10). |
| P-44 | Distribución | Cada versión se publica en GitHub Releases con su instalador. |
| P-45 | Metodología | Como *seguimiento-medicion*, en versión liviana: SDD, BDD, TDD, PR con plantilla, hitos como milestones y una nota de cierre por hito. Sin registro de uso de IA. |
