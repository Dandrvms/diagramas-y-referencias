Entendido. Esta aclaración cambia de manera importante el enfoque que debemos darle a la tesis. En particular, corrige tres supuestos de mi propuesta anterior: **el alcance de tu contribución al repositorio, el problema que justifica el desarrollo y la separación entre el editor de recetas y el sistema de ejecución**.

También queda claro que no necesitas que la tesis justifique el desarrollo de `malbec-s88` únicamente porque Malbec carecía de ese módulo. La ausencia del módulo es una circunstancia del proyecto de software; la necesidad de la industria es el problema que da sentido al trabajo.

Voy a tomar como referencia las siguientes distinciones.

# 1. El problema de investigación y el problema de implementación son cosas diferentes

Tu trabajo tiene dos contextos relacionados, pero que no deben confundirse.

## 1.1. El problema industrial: el punto de partida de la investigación

El escenario que describes es una planta de producción por lotes que no dispone de un sistema especializado de control batch.

En ese escenario intervienen operadores, HMI, PLC, recetas y áreas de producción. La coordinación del proceso depende de las capacidades de los sistemas existentes y de las prácticas operativas de la planta.

Las limitaciones que has identificado incluyen la rigidez del intercambio y la gestión de recetas, así como la pérdida de trazabilidad de determinadas actividades y eventos de producción.

Este debe ser el centro del planteamiento del problema.

La pregunta fundamental no es cómo llenar el vacío de un repositorio, sino cómo desarrollar un sistema que permita modelar procesos batch, gestionar sus recetas, integrarse con la infraestructura de automatización y proporcionar información sobre su ejecución.

### Sobre el acceso limitado a información industrial

Aquí hay que ser cuidadosos.

No necesitas afirmar que todas las plantas batch convencionales presentan las mismas deficiencias. Debes describir el problema industrial que sustenta tu proyecto, diferenciando las características generales de los procesos batch de las limitaciones que has podido constatar en el contexto de la empresa.

Las fuentes documentales, las conversaciones con los responsables técnicos y las observaciones que realmente hayas realizado pueden respaldar ese diagnóstico.

Si no has tenido acceso suficiente para caracterizar exhaustivamente una planta concreta, debes reconocer esa limitación. Puedes explicar el escenario de operación estudiado y las necesidades que motivan el desarrollo sin atribuirle a una instalación características que no hayas podido verificar.

### Sobre el primer diagrama

El diagrama con `Operador`, `HMI`, `PLC`, `Recetas` y `Area` es útil para explicar el escenario actual.

Sin embargo, recomendaría titularlo:

**Modelo conceptual del flujo de información y operación de una planta batch sin sistema de gestión batch especializado.**

Lo considero más preciso que utilizarlo como el único modelo de dominio de tu software.

¿Por qué?

Porque representa principalmente los participantes del escenario y sus interacciones: quién visualiza la información, quién selecciona las recetas, qué sistema las ejecuta y cómo se relacionan el área y el PLC.

El modelo de dominio de `malbec-s88` debe representar, además, los conceptos propios de ISA-88 y sus relaciones internas: jerarquía física, elementos procedimentales, pasos, transiciones, recetas, parámetros y demás conceptos relevantes para la implementación.

Por tanto, en la tesis tendrás **dos modelos distintos que cumplen funciones complementarias**:

1. El modelo conceptual del escenario industrial existente, utilizado para caracterizar el problema.
2. El modelo de dominio del sistema desarrollado, utilizado para explicar cómo se representa ese problema en el software.

También revisaría las multiplicidades del primer diagrama antes de incorporarlo a la versión definitiva. En UML, las multiplicidades representan cuántas instancias pueden relacionarse; no simplemente cuántas operaciones realiza cada actor.

# 2. La contribución de tu trabajo: el desarrollo integral de `malbec-s88`

Queda corregido el diagnóstico anterior: no debo presentar `api`, `data`, `core` o `plant` como componentes preexistentes que tú simplemente ampliaste.

Según aclaras, creaste `malbec-s88` desde cero y eres responsable de desarrollar sus componentes.

Esta circunstancia debe quedar explícita en el capítulo de resultados.

La distinción relevante es la siguiente:

| Componente | Responsabilidad dentro del proyecto | Contribución que debes documentar |
|---|---|---|
| `api` | Modelos del dominio y contratos entre componentes. | Diseño del modelo ISA-88, las interfaces y sus relaciones. |
| `data` | Adaptadores y mecanismos de persistencia. | Diseño del almacenamiento, serialización, recuperación e integración con los contratos. |
| `core` | Reglas de negocio y operaciones sobre el modelo. | Casos de uso, validaciones, operaciones de modificación y pruebas. |
| `plant` | Editor del modelo físico. | Representación de la jerarquía, edición, persistencia e interacción con el modelo. |
| `recipes` | Editor de recetas. | Edición procedimental, representación SFC, geometría, disposición gráfica y validaciones. |
| `batch` | Motor de ejecución y comunicación del sistema batch. | Integración con EPICS/PVA, supervisión, comandos, procesamiento de eventos y registro de ejecución, según lo que finalmente implementes. |

Esto también modifica la sección que había propuesto para el estado inicial.

No debes dedicar una parte importante de la tesis a enumerar funcionalidades supuestamente preexistentes en `malbec-s88`. Lo correcto es explicar que el proyecto se desarrolla dentro de un repositorio y un ecosistema tecnológico existentes, pero que el módulo objeto de estudio fue creado como parte del trabajo de grado.

Puedes documentar dos situaciones iniciales:

**Estado inicial industrial:** cómo se gestionan los procesos, las recetas y la supervisión en el escenario estudiado.

**Estado inicial tecnológico:** qué infraestructura proporciona el repositorio existente y qué componentes externos se aprovecharán para desarrollar el nuevo módulo.

El segundo contexto es importante para entender las restricciones e integraciones del proyecto, pero no reemplaza al primero.

---

# 3. La arquitectura objetivo: el editor y el runtime son componentes diferentes

El segundo diagrama permite aclarar una distinción fundamental que debe mantenerse en toda la tesis.

La aplicación tiene una parte orientada al diseño y otra orientada a la operación.

## 3.1. Design-time: configuración del modelo físico y las recetas

En esta parte se encuentra el trabajo que estás realizando en `plant` y `recipes`.

El ingeniero configura el modelo físico de la planta y define las recetas, sus pasos, transiciones y parámetros.

El editor SFC permite modificar la representación procedimental y su disposición gráfica. La información resultante debe poder almacenarse y recuperarse.

**El editor de recetas no es responsable de ejecutar la secuencia industrial.**

Su responsabilidad consiste en permitir la definición y modificación del proceso y conservar la información necesaria para utilizarlo posteriormente.

## 3.2. Runtime: ejecución, comunicación y supervisión

El módulo `batch` corresponde a una responsabilidad diferente.

Según la arquitectura que describes, este componente debe encargarse de establecer la comunicación con el entorno EPICS/PVA, intercambiar información y permitir que la aplicación supervise la ejecución del proceso.

El flujo esperado es:

1. El motor de runtime obtiene la receta que debe utilizar.
2. Se prepara la información necesaria para la ejecución.
3. El cliente de comunicación intercambia los comandos y datos requeridos con el servidor EPICS/PVA.
4. El servidor de Merlot participa en la integración con el controlador industrial.
5. El PLC ejecuta la lógica de control que le corresponde.
6. El sistema de supervisión recibe y procesa los eventos comunicados.
7. La visualización representa el estado de ejecución sobre la estructura gráfica de la receta.
8. Los eventos y resultados relevantes se registran para proporcionar trazabilidad.

Debes ajustar este flujo al comportamiento efectivo del sistema una vez implementado y validado.

Hay una distinción importante: el runtime puede ser *headless*, pero la supervisión necesita una interfaz visual. Eso no es contradictorio.

Lo recomendable es separar conceptualmente:

- **Motor de runtime:** coordina la ejecución desde el punto de vista del software, gestiona el estado y procesa la información recibida.
- **Cliente de comunicación:** gestiona el intercambio de datos con EPICS/PVA.
- **Interfaz de supervisión:** representa el estado del proceso y permite visualizar la ejecución.
- **Mecanismo de registro:** conserva los eventos y resultados que deben consultarse posteriormente.

Estos componentes pueden distribuirse entre módulos, servicios o procesos según la implementación definitiva. No necesitas convertir cada responsabilidad en un módulo Maven independiente.

En el diagrama actual convendría aclarar si el motor se ejecutará dentro de la JVM de Malbec o como una instancia independiente. La separación lógica entre motor e interfaz no obliga a que sean procesos separados.

## 3.3. El papel de Merlot, EPICS/PVA y PLC4X

Tu diagrama también permite distinguir correctamente el módulo batch de `malbec-s88` del `Communications Project`.

El flujo arquitectónico objetivo que has descrito es el siguiente:

**Malbec S88 → Communications Project y configuración → Merlot → PLC4X → PLC**

En este flujo, cada componente tiene una función diferente.

El proyecto S88 de Malbec define el modelo físico y la información asociada al proceso.

El `Communications Project`, perteneciente a otro proyecto y fuera del alcance de desarrollo que has definido, utiliza esa información para configurar las asociaciones entre áreas S88 y dispositivos físicos, generando `boot.db`.

Merlot actúa como pasarela de runtime. Según el diagrama, utiliza `boot.db` para configurar las asociaciones y el mecanismo de lectura y escritura implementado mediante `PlcGroups & Scan Engine`.

La interacción con el PLC pasa por Apache PLC4X.

**Por su parte, el módulo `batch` de Malbec es el cliente EPICS/PVA que supervisa la ejecución y envía los comandos que le corresponden.** No debe confundirse con el `Communications Project` ni representarse como una conexión directa al PLC mediante PLC4X si esa no es la arquitectura real.

Esta distinción debe reflejarse tanto en el diagrama de componentes como en los diagramas de secuencia y despliegue.

---

# 4. ¿Debes agregar conceptos al marco teórico si no estaban en el anteproyecto?

Mi recomendación es que sí, pero con una distinción clara entre los fundamentos necesarios para comprender el proyecto y los detalles específicos de la implementación.

**No necesitas modificar el anteproyecto aprobado únicamente porque la memoria final requiera desarrollar con mayor profundidad algunas bases teóricas.**

El anteproyecto establece el problema, los objetivos, el alcance previsto y la metodología. La tesis final desarrolla esos elementos, presenta las decisiones adoptadas y demuestra los resultados.

El anteproyecto ya contempla la definición de los modelos, los módulos de comunicación y las pruebas, además de la documentación técnica y los manuales de usuario. Anteproyecto Daniel Campos

Por tanto, ampliar las bases teóricas para explicar las tecnologías y los conceptos necesarios para entender esa implementación es coherente con el propósito de la memoria.

No obstante, si el cambio modifica sustancialmente los objetivos, el alcance o los compromisos aprobados, deberías consultarlo con tu tutor y seguir el procedimiento de la comisión.

## 4.1. Qué incorporaría al marco teórico

Añadiría los siguientes apartados al Capítulo II.

### A. ISA-88 y modelado de procesos batch

Mantendría el contenido fundamental que ya tienes sobre:

- Modelo físico.
- Modelo de control procedimental.
- Modelo de recetas.
- Jerarquías y relaciones entre los elementos.

Este es el fundamento conceptual del sistema y debe conservar una posición central.

### B. SFC como representación de la lógica procedimental

Explicaría los elementos que utiliza el editor:

- Pasos.
- Transiciones.
- Conexiones.
- Bifurcaciones y sincronizaciones, según el alcance del modelo.
- Condiciones y parámetros asociados al procedimiento.
- Diferencia entre la estructura del procedimiento y su representación visual.

Es importante identificar correctamente la norma o notación de referencia utilizada para SFC. No conviene afirmar que todos los detalles del editor gráfico derivan directamente de ISA-88.

### C. EPICS y PVAccess (PVA)

Incorporaría una introducción suficiente para entender la integración del runtime.

Puedes explicar qué papel desempeñan EPICS y PVA, cómo se identifican las variables o propiedades de proceso y qué significa que un cliente se suscriba a información y pueda enviar comandos.

No necesitas convertir el marco teórico en un manual del protocolo.

### D. Comunicación industrial mediante PLC4X y la función de Merlot

Explicaría las responsabilidades generales de PLC4X y Merlot dentro del ecosistema tecnológico.

En particular, debe quedar claro por qué se utiliza una pasarela y cómo se separa el intercambio de información a nivel de aplicación de la comunicación con los dispositivos físicos.

### E. Trazabilidad, eventos y registros de ejecución

Dado que la pérdida de trazabilidad es parte de la necesidad industrial que motiva el proyecto, estos conceptos merecen una sección propia.

Explica qué es un evento de ejecución, qué información puede registrar, por qué es importante mantener una secuencia temporal y cómo contribuyen los registros y reportes a la supervisión de un lote.

### F. Arquitectura y prácticas de desarrollo

Mantendría la explicación de la arquitectura en capas, los principios de Clean Architecture, los casos de uso, las pruebas y UML.

Estos temas deben apoyar las decisiones que documentarás posteriormente, no convertirse en una exposición teórica independiente del desarrollo.

## 4.2. Qué dejaría para el capítulo de resultados

La explicación específica de cómo has aplicado estos conceptos pertenece al Capítulo IV.

Por ejemplo:

| Tema | Marco teórico | Resultados e implementación |
|---|---|---|
| SFC | Conceptos, elementos y notación empleada. | Funcionamiento del editor, `SfcLayoutEngine`, cálculo geométrico y actualización de la representación. |
| EPICS/PVA | Fundamentos de comunicación y conceptos relevantes. | Arquitectura del cliente, suscripciones, variables utilizadas, comandos y tratamiento de respuestas. |
| Merlot y PLC4X | Responsabilidades generales dentro del ecosistema. | Interacciones, configuración y recorrido real de los datos. |
| Trazabilidad | Concepto y finalidad de los registros de ejecución. | Estructura de los eventos, generación de logs y reportes, persistencia y consultas. |
| Arquitectura | Principios y patrones utilizados. | Módulos de `malbec-s88`, dependencias, decisiones adoptadas y colaboración entre componentes. |

Así evitas dos extremos: un marco teórico demasiado pobre para explicar el sistema o uno excesivamente extenso que se convierte en un manual técnico.

---

# 5. Cómo adaptar RUP a tu proceso real

Sobre RUP, entiendo la exigencia académica. El anteproyecto aprobado ya presenta las cuatro fases y las relaciona con actividades concretas del proyecto. Anteproyecto Daniel Campos

La solución que recomiendo es utilizar RUP como **estructura principal para organizar y documentar el desarrollo**, explicando que la implementación fue iterativa e incorporó prácticas y principios arquitectónicos complementarios.

No necesitas afirmar que cada actividad se realizó una sola vez o que todos los artefactos existieron antes de escribir código.

Puedes reconstruir los modelos de análisis y diseño a partir de la implementación existente, siempre que los identifiques como documentación del diseño y puedas justificar las decisiones mediante el código, las pruebas y los registros disponibles.

La tesis podría organizar las actividades así:

| Fase RUP | Aplicación en tu proyecto |
|---|---|
| Inicio | Diagnóstico de la necesidad industrial, contexto, alcance, actores, objetivos, requisitos de alto nivel y riesgos iniciales. |
| Elaboración | Modelo de dominio ISA-88, especificación de requisitos, casos de uso, arquitectura candidata, diagramas de interacción y decisiones arquitectónicas fundamentales. |
| Construcción | Desarrollo de `api`, `data`, `core`, `plant` y `recipes`, seguido de la implementación de `batch` y su integración con la comunicación y la supervisión. |
| Transición | Validación funcional, pruebas de integración, pruebas controladas de comunicación, documentación técnica, manual de usuario y demostración final. |

La arquitectura en capas y los principios de Clean Architecture explican cómo organizaste el código. Los casos de uso explican cómo estructuraste las operaciones. TDD explica la práctica de desarrollo y validación aplicada cuando corresponda.

RUP organiza el ciclo de vida y los resultados del proyecto. No necesitas presentar estos enfoques como si fueran mutuamente excluyentes.

---

# 6. La estructura de la tesis que mantendría

Con las nuevas aclaraciones, conservaría la estructura académica general del ejemplo, pero modificaría el contenido del Capítulo IV para que el problema industrial y el desarrollo real de `malbec-s88` sean el centro del documento.

| Capítulo | Enfoque actualizado |
|---|---|
| **I. El problema** | Necesidad industrial, escenario de operación batch convencional, limitaciones observadas, formulación, objetivos, justificación, alcance y limitaciones. |
| **II. Marco teórico** | Procesos batch, ISA-88, recetas, SFC, EPICS/PVA, PLC4X, Merlot, trazabilidad, arquitectura de software y fundamentos necesarios para comprender la solución. |
| **III. Marco metodológico** | Técnicas utilizadas, fuentes de información, entorno de desarrollo y pruebas, metodología RUP y estrategia de validación. |
| **IV. Resultados** | Desarrollo organizado por las fases de RUP, desde el diagnóstico industrial y los requisitos hasta la implementación de todos los módulos y la validación. |
| **V. Conclusiones y recomendaciones** | Cumplimiento de cada objetivo, resultados demostrados, limitaciones reales y líneas de evolución. |

Dentro del Capítulo IV mantendría la siguiente organización:

**4.1. Fase de inicio:** contexto industrial, flujo operativo actual, estado inicial tecnológico, modelo de dominio conceptual, requisitos, actores, casos de uso y riesgos.

**4.2. Fase de elaboración:** modelo de dominio ISA-88, arquitectura candidata, separación de módulos, dependencias, persistencia, decisiones de diseño y diagramas de interacción.

**4.3. Fase de construcción:** desarrollo de `api`, `data`, `core`, `plant`, `recipes` y `batch`; representación SFC; comunicación con EPICS/PVA; runtime; supervisión, logs y reportes.

**4.4. Fase de transición:** pruebas unitarias, pruebas de integración, pruebas de caja negra, escenarios de extremo a extremo, validación con simulador o entorno industrial y documentación final.

La regla que mantendría a lo largo de todos estos apartados es que la arquitectura, las clases y los diagramas deben corresponderse con el estado real del sistema. Si una parte permanece pendiente, debe aparecer como pendiente; si una decisión se reconstruyó durante la documentación, debe explicarse como tal.

## Mi conclusión

Con la información adicional, el trabajo queda mejor definido: no es una tesis sobre un editor SFC aislado ni sobre la ampliación de un módulo que ya existía. Es el desarrollo de un sistema de gestión batch basado en ISA-88, dentro de un ecosistema industrial de software existente, que abarca la definición del modelo físico y procedimental, la edición de recetas, la comunicación y supervisión durante la ejecución y la trazabilidad de los procesos.

Tu contribución en `malbec-s88` debe quedar documentada como el desarrollo integral de esos componentes, especificando qué implementaste y cómo se relacionan.

La memoria debe partir de la necesidad industrial, utilizar la teoría para fundamentar las decisiones y recorrer las fases de RUP para explicar el proceso de desarrollo y demostrar los resultados. El repositorio y su arquitectura son esenciales para entender la solución, pero **el problema industrial que resuelve es lo que debe dar unidad a toda la tesis**.
