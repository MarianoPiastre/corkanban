# 01 · Metodología

Corkanban usa las mismas prácticas que *seguimiento-medicion*, en versión liviana. Este capítulo explica qué es
cada una, para qué sirve y cómo se ve en este proyecto.

## La idea central: la cadena de trazabilidad

Cada línea de código tiene que poder responder «¿por qué existo?». Para eso, cada funcionalidad recorre esta cadena:

**Requerimiento → Historia de usuario → Spec SDD → Criterios de aceptación → Escenarios BDD → Tests → Código**

Si mañana algo falla, se puede seguir el hilo hacia atrás: el test que falla dice qué criterio no se cumple, el
criterio dice qué historia se rompió y la historia dice qué requerimiento del documento quedó en falta.

## Un ejemplo de punta a punta: crear una tarea

Los nombres de este ejemplo son ilustrativos; el código real puede quedar distinto.

### 1. Requerimiento

En [`docs/requerimientos.md`](../requerimientos.md), sección 3:

> **RF-09** · Crear con un botón «+» en la celda Pendiente de cada fila. La tarea nueva entra al final de Pendiente de
> esa fila.

El documento de requerimientos describe **el producto entero**. Es grande y no dice cómo programar cada parte.

### 2. Historia de usuario (un issue de GitHub)

Una historia es un pedazo chico del producto, contado desde quien lo usa:

> **Como** usuario del tablero
> **quiero** crear una tarea con el «+» de una fila
> **para** anotar algo pendiente sin salir del escritorio

Una historia tiene que poder terminarse en pocos días y dejar algo que funcione. Por eso conviene cortarlas en
**rebanadas verticales**: un poco de lógica, un poco de interfaz y un poco de guardado, en lugar de «primero toda la
lógica, después toda la interfaz».

### 3. Especificación SDD (`specs/HU-02-crear-tarea.md`)

**SDD** (*Spec-Driven Development*, desarrollo guiado por especificaciones) significa escribir **antes de programar**
qué tiene que hacer el código: entradas, salidas, reglas, casos límite y errores. Obliga a pensar los bordes
(¿qué pasa con un título vacío? ¿y con uno de 141 caracteres?) cuando todavía es barato cambiar de idea.

Un pedazo de esa spec:

| Dato | Tipo | Obligatorio | Validación |
|---|---|---|---|
| Título | texto | Sí | Sin espacios al principio ni al final; entre 1 y 140 caracteres |
| Fila | id de fila | Sí | Tiene que existir en el tablero |

- **RN1:** la tarea nueva entra en la columna Pendiente de su fila.
- **RN2:** queda al final de esa celda.
- **RN3:** los campos opcionales vacíos no se guardan.

### 4. Criterios de aceptación

Son las condiciones que el usuario comprobaría para decir «sí, esto funciona». Se numeran por historia:

- **CA-02.1:** la tarea aparece al final de Pendiente de la fila elegida, y solo ahí.
- **CA-02.2:** un título vacío no crea nada.

No confundir con las **PA-xx** de la sección 9 de los requerimientos: esas son pruebas del producto completo, que se
hacen a mano en la PC al cerrar cada hito.

### 5. Escenarios BDD (`features/hu-02-crear-tarea.feature`)

**BDD** (*Behavior-Driven Development*, desarrollo guiado por comportamiento) escribe cada criterio como un ejemplo
concreto, en español, con un formato llamado **Gherkin**:

```gherkin
# language: es
@HU-02
Característica: Crear una tarea

  @CA-02.1
  Escenario: La tarea nueva entra al final de Pendiente
    Dado un tablero con las filas "Trabajo" y "Facultad"
    Y la tarea "Enviar informe" en Pendiente de "Trabajo"
    Cuando creo la tarea "Llamar al banco" en la fila "Trabajo"
    Entonces Pendiente de "Trabajo" tiene "Enviar informe" y "Llamar al banco", en ese orden
    Y Pendiente de "Facultad" está vacía
```

Lo valioso es que **se ejecuta**: una herramienta (acá, **cucumber-js**; en seguimiento-medicion era **godog**) lee
cada línea y la conecta con una función de JavaScript llamada **paso** (*step*):

```js
// features/pasos/hu-02.pasos.js
// Las palabras Dado/Cuando/Entonces del .feature corresponden a Given/When/Then.
When('creo la tarea {string} en la fila {string}', function (titulo, fila) {
  this.tablero = crearTarea(this.tablero, { fila, titulo });
});
```

Así, el criterio de aceptación deja de ser texto suelto: si alguien rompe la funcionalidad, el escenario falla.

Cada historia lleva escenarios de cuatro tipos: **normal** (el camino feliz), **alternativo** (otra situación
válida), **límite** (bordes: vacío, máximo, medianoche) y **error** (datos inválidos).

### 6. Tests con TDD

**TDD** (*Test-Driven Development*, desarrollo guiado por tests) es un ciclo corto que se repite muchas veces:

1. **RED:** escribís un test que falla, porque el código todavía no existe o no hace eso.
2. **GREEN:** escribís el código mínimo para que pase. Nada más.
3. **REFACTOR:** mejorás el código (nombres, duplicación) sin cambiar lo que hace. Los tests te avisan si rompiste
   algo.

Cada fase es un commit separado, y así el historial de git demuestra que el test vino antes que el código:

```text
test(HU-02): RED - un título vacío no crea la tarea
feat(HU-02): GREEN - crearTarea rechaza el título vacío
refactor(HU-02): extrae la validación del título
```

En JavaScript se usa el runner de tests que ya trae Node, sin instalar nada:

```js
// test/logica/tareas.test.js
import { test } from 'node:test';
import assert from 'node:assert/strict';
import { crearTarea } from '../../src/logica/tareas.js';

test('un título vacío no crea la tarea', () => {
  const tablero = tableroDePrueba();
  assert.throws(() => crearTarea(tablero, { fila: 'Trabajo', titulo: '   ' }), /título/);
});
```

Se corre con `node --test`. En Rust, los tests se escriben en el mismo archivo del código y se corren con
`cargo test`.

**¿Por qué TDD y BDD a la vez?** Se complementan. Los escenarios BDD prueban la historia desde afuera, con el
lenguaje del usuario. Los tests TDD prueban cada función por dentro, con más detalle y casos.

### 7. Código

Recién acá aparece `crearTarea`. Como la spec, los escenarios y los tests ya dicen qué tiene que hacer, el código
suele salir más simple.

## Lo que no se puede probar con tests: la parte de Windows

La lógica del tablero (crear, mover, finalizar, plazos) son **funciones puras**: reciben datos y devuelven datos, sin
tocar la pantalla ni el disco. Por eso se prueban fácil y rápido.

La integración con Windows (poner la ventana como fondo, recibir el ratón detrás de los íconos, la bandeja) depende
del escritorio real. No hay forma razonable de que un test automático simule «el escritorio de Windows con sus
íconos». Esas partes se verifican con **pruebas manuales**: la spec de la historia lista qué probar en la PC, el PR
dice qué se probó y el cierre de cada hito registra las PA-xx.

Para que esa parte sea lo más chica posible, el diseño separa la lógica del tablero (mucha, probada con tests) de la
integración con Windows (poca, probada a mano). Este principio aparece en los requerimientos como RT-04.

## Spikes: cuando primero hay que averiguar

Un **spike** es una prueba técnica para responder una duda antes de construir: «¿se puede poner una ventana de Tauri
como fondo y que reciba el ratón?». No lleva TDD, porque el código es exploratorio y después se descarta. Lo que
queda es un documento con lo que se aprendió. El hito 0 es un spike, igual que `spike-supabase-auth.md` en
seguimiento-medicion.

## Ramas, commits y Pull Requests

- Cada issue se trabaja en su propia rama (`feature/HU-02-crear-tarea`), nunca directo en `main`.
- Los commits siguen [Conventional Commits](https://www.conventionalcommits.org/es/): `tipo(ID): descripción`.
- Al terminar, se abre un **Pull Request** (PR) contra `main`. La plantilla pide la trazabilidad, la checklist de la
  Definition of Done y una sección «Qué se aprende acá».
- `main` está **protegida**: no acepta pushes directos, solo PRs. Cuando exista el CI, además va a exigir que pase.
- Se mergea **sin squash**: el squash junta todos los commits en uno y borraría la evidencia RED → GREEN → REFACTOR.

Trabajando solo, nadie más revisa el PR. Lo revisan el CI (que corre todos los tests) y vos, con la explicación del
PR.

## Hitos, tablero y cierre

- Los **hitos** de los requerimientos son **milestones** de GitHub: agrupan los issues de cada etapa.
- El **tablero** del proyecto en GitHub muestra los issues en columnas, como un kanban (sí, un kanban para hacer un
  kanban).
- Al cerrar un hito se escribe una nota corta en `docs/hitos/`: qué se terminó, qué PA-xx pasaron, qué salió bien,
  qué mejorar y qué aprendiste. Junta en un solo documento la *review* y la *retro* de Scrum.

## Definition of Ready y Definition of Done

Son dos checklists acordadas de antemano ([`definition-of-ready-done.md`](../definition-of-ready-done.md)):

- **Ready:** cuándo una historia está lista para empezar (tiene criterios, requerimientos y dependencias claras).
- **Done:** cuándo está terminada de verdad (spec, escenarios, tests, cobertura, CI y pruebas manuales).

Sirven para no engañarse: «funciona en mi PC» no es lo mismo que «está terminada».
