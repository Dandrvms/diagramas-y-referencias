Después de revisar el anteproyecto, la estructura del repositorio de Malbec y el ejemplo de tesis, mi recomendación es que estructures tu trabajo de grado como una **memoria de ingeniería de software que documente el diagnóstico inicial, el análisis del dominio ISA-88, las decisiones arquitectónicas, la implementación de los módulos, la integración con el entorno industrial y la validación del sistema**.

La idea no es limitarse a describir las clases que programaste. Debes demostrar cómo identificaste el problema, cómo tradujiste los requisitos industriales en modelos y componentes de software, por qué tomaste determinadas decisiones arquitectónicas, cómo construiste la solución y qué evidencias permiten afirmar que funciona.

Además, conviene aprovechar lo que ya está implementado. Tu módulo `malbec-s88` no parte de cero: el repositorio contiene una separación entre modelos y contratos, casos de uso, persistencia, edición del modelo físico e interfaces para la edición de recetas. Eso proporciona una base importante para explicar la arquitectura y planificar el trabajo pendiente. dandrvms-plc4x-extras-01dbf6931…

Voy a dividir el plan en seis partes:

1. Diagnóstico del proyecto y determinación de su arquitectura.
2. Estrategia para organizar el trabajo y terminar la implementación.
3. Estructura exacta de la tesis, capítulo por capítulo.
4. Diagramas, modelos, fragmentos de código y pruebas que deberás documentar.
5. Plan de validación y documentación técnica y de usuario.
6. Conclusiones, recomendaciones y matriz de cumplimiento de objetivos.

# 1. Diagnóstico inicial: qué estás construyendo y qué debes demostrar

## 1.1. El objetivo del trabajo de grado

El anteproyecto establece como objetivo general:

> Desarrollar un sistema de control de procesos por lotes bajo el estándar ISA-88 acoplado a Apache PLC4X.

Los objetivos específicos se articulan alrededor de la identificación de los requisitos ISA-88, la definición de los requisitos funcionales y no funcionales, el diseño de los módulos, su implementación y la validación mediante recetas de prueba en un entorno controlado. Anteproyecto Daniel Campos

Esto tiene una consecuencia importante para la tesis: **el trabajo no debe presentarse simplemente como el desarrollo de un editor gráfico de recetas**.

El editor es una parte de un sistema mayor. El valor del proyecto está en relacionar los diferentes elementos de un proceso industrial:

- La jerarquía física de la planta.
- La definición procedimental del proceso.
- Las recetas y sus parámetros.
- La asociación entre las fases de una receta y los equipos disponibles.
- La comunicación con el entorno de automatización.
- La supervisión de la ejecución.
- La generación de información sobre lo ocurrido durante un lote.

El alcance descrito en el anteproyecto contempla precisamente la configuración de la planta, la gestión de recetas, su ejecución, su supervisión en tiempo real y la trazabilidad de los procesos. Anteproyecto Daniel Campos

Por tanto, la tesis debe contar la historia completa del diseño y construcción de ese sistema, distinguiendo claramente las funcionalidades terminadas de las que todavía están pendientes.

## 1.2. Qué muestra actualmente el repositorio

El archivo de estructura y código que proporcionaste permite identificar los siguientes módulos dentro de `malbec-s88`.

| Módulo | Responsabilidad que se desprende de la estructura | Importancia para la tesis |
|---|---|---|
| `api` | Modelos del dominio, clases relacionadas con ISA-88, interfaces de almacenamiento y repositorios, referencias e información de disposición gráfica. | Explicar el modelo de dominio y los contratos entre componentes. |
| `core` | Casos de uso, validaciones, reglas de conformidad, edición de recetas y operaciones sobre el modelo físico. | Explicar la lógica de aplicación y las reglas de negocio. |
| `data` | Adaptadores de almacenamiento y repositorios para distintos formatos, junto con almacenamiento basado en archivos de NetBeans. | Explicar cómo se leen, guardan y transforman los modelos. |
| `plant` | Acciones, nodos, diálogos y componentes que permiten configurar y editar el modelo físico. | Documentar el editor de planta implementado. |
| `recipes` | Apertura y edición de recetas, vistas gráficas y tabulares, representación SFC, distribución de nodos y otras funciones del editor. | Documentar el editor de recetas, su diseño y sus pruebas. |
| `project` | Integración con el sistema de proyectos de NetBeans y sus componentes de configuración. | Explicar cómo se incorpora la funcionalidad en la plataforma existente. |
| `batch` | Módulo que puede servir de punto de partida para las funcionalidades de ejecución batch. | Lugar candidato para la integración de runtime, aunque su responsabilidad final debe decidirse arquitectónicamente. |

La estructura distingue estos siete módulos como parte del proyecto Maven de `malbec-s88`. dandrvms-plc4x-extras-01dbf6931… dandrvms-plc4x-extras-01dbf6931…

En el módulo `api` ya aparecen clases como `S88PlantModel`, `S88PlantSnapshot`, `S88Recipe`, `S88MasterRecipe`, `S88ControlRecipe`, `S88ProcedureLogic`, `S88ProcedureStep` y `S88ProcedureTransition`. El módulo `core`, por su parte, contiene numerosos casos de uso, entre ellos `CreateElementUseCase`, `CreateMasterRecipeUseCase`, `BindRecipeStepUseCase` y `EditProcedureLogicUseCase`.

También existe una batería de pruebas para el modelo, los casos de uso y las operaciones de edición. dandrvms-plc4x-extras-01dbf6931…

El editor de recetas incluye componentes para la edición de modelos, la representación gráfica SFC, el enrutamiento de conexiones, la edición de pasos, la navegación y las operaciones de deshacer y rehacer. Su estructura de pruebas contiene, entre otras, clases como `RecipeEditorModelTest`, `RecipeChartSceneTest` y `SfcLayoutEngineTest`. dandrvms-plc4x-extras-01dbf6931…

Por tanto, puedes sustentar buena parte del desarrollo con elementos concretos del código, en lugar de inventar una arquitectura teórica que no corresponda con el sistema.

### Una precaución importante sobre el diagnóstico

El archivo que proporcionaste es una instantánea de la estructura y el código. No contiene, por sí mismo, toda la historia de cómo se encontraba el proyecto cuando comenzaste a trabajar en él.

Por eso, debes distinguir tres estados:

**Estado preexistente:** funcionalidades que ya estaban disponibles en Malbec antes de tu intervención.

**Estado de la implementación propia:** funcionalidades que has desarrollado, modificado o completado dentro de `malbec-s88`.

**Estado objetivo:** funcionalidades que deben estar disponibles cuando entregues el trabajo de grado.

No atribuyas al proyecto original clases o módulos que hayas creado tú. Si tienes acceso al historial de Git, las primeras versiones del código, ramas o entregas iniciales, utilízalos para establecer ese punto de partida.

Esta distinción será fundamental para el capítulo de diagnóstico y para explicar cuál fue tu contribución.

## 1.3. El estado de desarrollo que debemos tomar como referencia

Según lo que indicas, el estado actual del módulo puede resumirse así:

| Componente | Estado de partida de este plan | Trabajo que debe documentarse |
|---|---|---|
| Modelo de dominio ISA-88 | Existen clases de modelo y contratos en el repositorio. Debe verificarse cuáles forman parte de tu contribución. | Relaciones, responsabilidades, invariantes y decisiones del modelo. |
| Editor del modelo físico | Implementado. | Diseño, interacción con el modelo, persistencia y pruebas. |
| Editor de recetas | En desarrollo. | Modelo procedimental, edición de pasos y transiciones, parámetros, enlaces y representación SFC. |
| Persistencia | Existen contratos y proveedores de almacenamiento en el repositorio. | Formatos utilizados, serialización, lectura, escritura y pruebas de integridad. |
| Comunicación de runtime | Pendiente según tu descripción. | Integración con EPICS, intercambio de información, comandos y gestión de errores. |
| Supervisión en ejecución | Pendiente según tu descripción. | Estados de ejecución, visualización del paso activo y actualización de datos. |
| Logs y reportes | Pendientes según tu descripción. | Registro cronológico de eventos, resultados y generación de reportes. |
| Simulación | Posible componente de apoyo a las pruebas. | Simulación controlada de estados, eventos y respuestas de comunicación. |
| Validación integral | Debe completarse sobre el alcance finalmente implementado. | Pruebas unitarias, integración, caja negra y escenarios de extremo a extremo. |
| Documentación | Por elaborar o completar. | Manual técnico, manual de usuario y memoria del trabajo de grado. |

Esta tabla debe actualizarse conforme avances. En la tesis final, la columna de estado tendrá que reflejar los resultados reales, no únicamente el plan previsto.

---

# 2. La arquitectura: cómo explicarla correctamente en la tesis

Este es uno de los apartados más importantes, porque condiciona la forma en que presentarás todos los demás.

## 2.1. Qué arquitectura parece utilizar actualmente `malbec-s88`

Mi lectura de la estructura es que estás trabajando con una **arquitectura modular en capas que incorpora principios de Clean Architecture y separación de responsabilidades mediante casos de uso, pero no necesariamente una implementación estricta de Clean Architecture**.

La organización del proyecto sugiere esta separación:

- **Modelo y contratos:** `api`.
- **Reglas de aplicación y casos de uso:** `core`.
- **Persistencia e integración con formatos de datos:** `data`.
- **Adaptación a la plataforma y edición gráfica:** `plant`, `recipes` y `project`.
- **Ejecución batch:** módulo `batch`, todavía por completar según tu descripción.

Esta es una interpretación arquitectónica de la estructura actual, no una afirmación de que el proyecto implemente formalmente todos los principios de Clean Architecture.

Para justificarla correctamente, documenta las dependencias reales entre los módulos, sus interfaces públicas y los puntos donde se produce el acoplamiento con NetBeans.

### Principios que sí conviene explicar

**Separación de responsabilidades.** El modelo que representa una receta no debería tener que conocer cómo se dibuja un nodo en la pantalla.

**Casos de uso.** Las operaciones de aplicación deberían expresar acciones relevantes para el dominio, como crear un elemento, modificar una receta, vincular un paso o establecer parámetros.

**Desacoplamiento del almacenamiento.** El núcleo no debería necesitar conocer los detalles de cada formato de archivo para aplicar las reglas del modelo.

**Separación entre dominio y presentación.** El hecho de que una receta tenga un paso y una transición es una propiedad del modelo procedimental; que estos se dibujen en determinadas coordenadas pertenece a la representación gráfica.

**Testabilidad.** Los casos de uso y las reglas del modelo deberían poder verificarse sin tener que iniciar toda la aplicación gráfica.

Estos principios se pueden relacionar con clases reales del proyecto y con sus pruebas.

## 2.2. Cómo presentar la relación entre Clean Architecture, capas, casos de uso y TDD

No presentes estos conceptos como si fueran cuatro arquitecturas independientes.

Tienen responsabilidades diferentes.

| Concepto | Aplicación en tu proyecto |
|---|---|
| Arquitectura en capas | Organiza la solución en responsabilidades, desde el dominio y la lógica hasta los adaptadores y la interfaz. |
| Clean Architecture | Proporciona principios para reducir el acoplamiento y controlar la dirección de las dependencias. |
| Casos de uso | Organizan las operaciones de aplicación, por ejemplo, crear una receta, modificar su estructura o vincular un paso con un equipo. |
| Test-Driven Development (TDD) | Es una práctica de desarrollo en la que se escribe una prueba antes de implementar el comportamiento que la satisface. |
| RUP | Proporciona un marco de ciclo de vida y una estructura de trabajo que puedes utilizar para presentar los requisitos, el análisis, el diseño, la construcción y la transición. |
| UML | Es el lenguaje gráfico que permite documentar la estructura y el comportamiento de la solución. |

La tesis debe explicar cómo se complementan estas ideas.

Por ejemplo, puedes tener un caso de uso de aplicación, ubicado en `core`, que utiliza un modelo definido en `api` y se verifica mediante una prueba unitaria. La interfaz gráfica invoca esa funcionalidad, pero no debería duplicar sus reglas.

Ese ejemplo conecta arquitectura, implementación y pruebas de una forma concreta.

## 2.3. Una cuestión especialmente importante: el editor SFC no es el runtime SFC

En la memoria debes separar claramente dos responsabilidades que visualmente pueden parecer similares.

**La representación del SFC** calcula dónde aparecen los nodos, cómo se dibujan las conexiones y cómo se distribuye el gráfico.

**La ejecución de la receta** determina cuál es el estado del proceso, qué pasos están activos, qué transiciones se han satisfecho y cómo se reflejan las señales recibidas del sistema de control.

Por tanto, `SfcLayoutEngine` pertenece al problema de disposición gráfica del diagrama. No deberías presentarlo como el motor que ejecuta la receta industrial.

El modelo procedimental debe contener la estructura y semántica del proceso. El runtime será otra responsabilidad, encargada de coordinar los comandos y los datos recibidos, siempre de acuerdo con el comportamiento que realmente permita el controlador.

Esta distinción también te permitirá justificar por qué el runtime debe diseñarse como un componente separado del editor.

---

# 3. Roadmap de desarrollo: en qué orden deberías trabajar

Recomiendo organizar el trabajo en ocho bloques consecutivos. No todos requieren el mismo esfuerzo, pero el orden reduce el riesgo de tener una interfaz de ejecución construida sobre un modelo de recetas todavía inestable.

## Fase 1. Cerrar el diagnóstico y fijar el alcance

**Objetivo:** establecer exactamente qué existe, qué falta y qué vas a entregar.

### Actividades

1. Identificar qué clases pertenecían al proyecto antes de tu intervención.
2. Identificar las funcionalidades que ya implementaste en el editor físico.
3. Determinar qué funcionalidades del editor de recetas están terminadas, incompletas o todavía no existen.
4. Revisar los módulos Maven, sus dependencias y los servicios que utiliza NetBeans.
5. Ejecutar las pruebas actuales y registrar los resultados iniciales.
6. Construir una matriz de funcionalidades: existente, modificada, pendiente o descartada.
7. Confirmar con los responsables del entorno industrial el comportamiento esperado del runtime.

### Entregables

Una descripción del estado inicial, un inventario de módulos, una matriz de alcance y una lista priorizada de requisitos.

No empieces a redactar conclusiones ni a cerrar la arquitectura del runtime hasta tener esta visión.

## Fase 2. Definir los requisitos y los contratos de runtime

**Objetivo:** que las funcionalidades pendientes tengan una especificación verificable.

Antes de empezar a implementar la comunicación, necesitas responder:

- Qué datos de una receta se deben enviar al controlador.
- Qué variables de proceso deben leerse.
- Cómo se representa el paso actualmente ejecutado.
- Qué estados del lote deben mostrarse.
- Qué comandos de operación se permiten.
- Cómo se reconoce que un comando fue aceptado o rechazado.
- Qué información necesita el registro de ejecución.
- Qué ocurre si se pierde la comunicación.
- Cómo se distinguirán los errores de comunicación de los estados de fallo del proceso.

### Entregables

Una especificación de requisitos, los casos de uso del runtime, un modelo inicial de estados y una matriz de riesgos.

**Decisión crítica:** debes confirmar el papel exacto de EPICS, PLC4X y Merlot en la instalación real.

El anteproyecto plantea integrar el módulo de comunicación con Merlot y emplear PLC4X para el intercambio de datos con controladores. Anteproyecto Daniel Campos

Ahora indicas que el runtime se comunicará con un servidor EPICS conectado a un PLC real. Debes documentar el camino de comunicación efectivo y aclarar cómo encaja con lo propuesto originalmente. Si EPICS es la interfaz real de comunicación, no dibujes una conexión directa entre Malbec y PLC4X que no exista en la implementación.

Si existen varios mecanismos, representa cada uno y explica su función.

## Fase 3. Consolidar el editor de recetas

El runtime dependerá directamente de la calidad del modelo procedimental. Por eso recomiendo cerrar primero una versión funcional del editor.

### Funcionalidades prioritarias

1. Crear y cargar una receta.
2. Editar sus propiedades.
3. Añadir y eliminar pasos.
4. Crear transiciones y conexiones.
5. Representar las bifurcaciones y sincronizaciones que contemple el modelo.
6. Configurar condiciones de transición.
7. Asociar pasos con equipos o fases disponibles.
8. Configurar los parámetros de receta.
9. Validar la estructura y las referencias.
10. Guardar, cerrar y volver a abrir la receta.
11. Verificar que las operaciones de deshacer y rehacer preservan la consistencia.

### Criterio para dar por finalizada esta fase

Una receta de prueba debe poder crearse, guardarse, cerrarse y volver a abrirse sin perder su información ni producir una estructura inválida.

Las pruebas deben cubrir tanto el modelo como la interfaz gráfica.

### Entregables

El editor de recetas funcional, los casos de uso correspondientes, las reglas de validación y un conjunto reproducible de recetas de prueba.

## Fase 4. Implementar el modelo de ejecución y un simulador

Esta fase es particularmente importante.

Mi recomendación es que desarrolles un **simulador antes de conectar el runtime con el PLC real**. No tiene que ser un simulador industrial completo. Basta con que represente de forma controlada los estados, transiciones, parámetros y eventos que necesita la aplicación para probar su comportamiento.

El simulador servirá para verificar la lógica de supervisión sin depender de la disponibilidad del servidor EPICS ni de la instalación física.

### Separación conceptual recomendada

El sistema debería distinguir, al menos, tres cosas:

**Definición de la receta:** qué pasos, transiciones, parámetros y asociaciones contiene.

**Sesión de ejecución:** qué receta concreta se está ejecutando, cuándo empezó, cuál es su estado actual y qué paso está activo.

**Eventos de ejecución:** qué ocurrió durante esa sesión, cuándo ocurrió y qué información se recibió o se generó.

No introduzcas el estado vivo de ejecución directamente en el modelo de definición de la receta sin una razón arquitectónica explícita.

Una estructura mínima podría incluir conceptos como los siguientes:

| Concepto propuesto | Responsabilidad |
|---|---|
| `RecipeExecutionSession` | Representar una ejecución concreta de una receta. |
| `ExecutionState` | Identificar el estado actual de la sesión. |
| `StepExecutionState` | Representar el estado de un paso durante esa ejecución. |
| `ExecutionEvent` | Registrar un cambio de estado, una respuesta, un error o una medición relevante. |
| `ExecutionCommand` | Expresar una orden solicitada por el usuario. |
| `RuntimeGateway` | Definir el contrato para intercambiar comandos y datos con el sistema de ejecución. |
| `ExecutionLogRepository` | Definir cómo se almacenan y consultan los eventos de ejecución. |

Son propuestas de diseño, no clases que debas asumir que ya existen en el repositorio.

### Entregables

Un modelo de runtime, un contrato de comunicación, un simulador que implemente ese contrato y pruebas unitarias sobre el comportamiento esperado.

## Fase 5. Integrar el runtime con EPICS

Una vez que el modelo y el simulador estén probados, implementa el adaptador de comunicación.

La idea es que el resto de la aplicación no dependa de los detalles de transporte ni de las particularidades del servidor EPICS.

El núcleo debe expresar lo que necesita hacer; el adaptador debe resolver cómo se realiza esa operación en el entorno de comunicación elegido.

### Actividades

1. Establecer la conexión con el servidor.
2. Leer las variables requeridas por el proceso.
3. Implementar el envío de los comandos que se hayan especificado y autorizado.
4. Asociar los datos recibidos con la sesión de ejecución correspondiente.
5. Gestionar errores, valores inválidos, tiempos de espera y pérdida de comunicación.
6. Definir qué ocurre cuando no se puede confirmar el resultado de un comando.
7. Verificar que los mensajes repetidos o recibidos fuera de orden no corrompen el estado de la sesión.
8. Registrar los errores y resultados relevantes.

### Precaución industrial

Los comandos que afectan al controlador deben especificarse con los responsables de automatización y probarse primero en un entorno controlado.

No debes asumir que cualquier comando puede repetirse automáticamente tras un tiempo de espera: antes es necesario determinar si la operación es idempotente y cómo se confirma su resultado. Un resultado de comunicación incierto no equivale necesariamente a que el controlador no haya ejecutado la acción.

### Entregables

Un adaptador probado, una matriz de señales y comandos, una descripción del manejo de fallos y pruebas de integración con el entorno disponible.

## Fase 6. Implementar supervisión, logs y reportes

Esta fase convierte una herramienta de edición en una herramienta que permite observar lo que sucede durante la ejecución.

### Funcionalidades

- Mostrar el estado de la receta o lote.
- Identificar el paso activo.
- Reflejar los cambios de estado en el diagrama SFC.
- Presentar los valores relevantes del proceso.
- Mostrar mensajes de error y pérdida de comunicación.
- Registrar las marcas temporales de los eventos.
- Consultar la secuencia de sucesos de una ejecución.
- Generar un reporte de ejecución con los resultados y eventos que realmente hayan podido registrarse.

### Regla arquitectónica

La interfaz debe visualizar el estado del runtime, no constituirse en el lugar donde se concentra toda la lógica de ejecución.

Por ejemplo, si el estado de un paso cambia, el modelo de ejecución debe actualizarse de acuerdo con la información validada. Después, la interfaz reacciona al cambio y actualiza el diagrama.

El registro de eventos también debería mantenerse separado del modelo de definición de recetas.

### Entregables

Una vista de supervisión, un mecanismo de registro de eventos y un reporte reproducible a partir de los datos guardados.

## Fase 7. Integrar y validar el sistema completo

Aquí debes verificar un flujo de trabajo completo, no solamente que cada ventana funcione por separado.

Un escenario de referencia sería:

1. Crear una planta.
2. Configurar el equipo necesario.
3. Crear una receta.
4. Definir sus pasos y transiciones.
5. Asociar los pasos con los equipos correspondientes.
6. Configurar los parámetros.
7. Validar la receta.
8. Guardar y volver a cargarla.
9. Iniciar una ejecución en el simulador o entorno controlado.
10. Comprobar que el sistema identifica el paso activo.
11. Provocar un evento de proceso y comprobar su registro.
12. Finalizar la ejecución.
13. Generar y revisar el reporte.

Si el entorno industrial está disponible y las condiciones de seguridad lo permiten, repite después un subconjunto representativo mediante la integración real.

## Fase 8. Cerrar la documentación y la tesis

No dejes la documentación para el final absoluto. Debes redactarla a medida que cierres cada módulo y verificar que corresponde con la implementación entregada.

La documentación final debería incluir:

- Memoria del trabajo de grado.
- Manual de instalación y configuración.
- Manual de arquitectura y desarrollo.
- Manual de usuario.
- Matriz de requisitos y pruebas.
- Diagramas actualizados.
- Evidencias de pruebas.
- Instrucciones para ejecutar la demostración controlada.

---

# 4. Estructura exacta propuesta para la tesis

El ejemplo de tesis organiza el trabajo en cinco grandes partes: planteamiento del problema, marco teórico, marco metodológico, resultados y conclusiones con recomendaciones. Dentro de resultados desarrolla los requisitos, los casos de uso, el análisis, la arquitectura, el diseño, la implementación y las pruebas. TESIS

Te recomiendo conservar esa estructura general, adaptando el contenido y manteniendo una numeración uniforme.

## Elementos preliminares

Antes del Capítulo I, incluye los elementos que exija el reglamento de la universidad y que aparecen, en buena parte, en el ejemplo:

- Portada.
- Página de aprobación y jurado, según corresponda.
- Resolución, si aplica.
- Dedicatoria y agradecimientos, si deseas incluirlos.
- Resumen.
- Índice general.
- Índice de tablas.
- Índice de figuras.
- Lista de siglas y abreviaturas.

El resumen definitivo debe redactarse después de terminar el sistema y documentar sus resultados.

### ¿Debe existir una introducción separada?

El ejemplo que aportaste comienza directamente con el Capítulo I y no tiene un capítulo independiente titulado *Introducción*. Por tanto, si la normativa de tu universidad sigue ese formato, no necesitas inventar un capítulo adicional.

Si el reglamento sí exige una introducción independiente, puedes añadir una breve presentación del contexto, el objetivo, el alcance y la organización del documento antes del Capítulo I.

En cualquier caso, no dupliques todo el planteamiento del problema en dos secciones casi idénticas.

# CAPÍTULO I. EL PROBLEMA

Este capítulo debe explicar por qué se necesita el sistema y qué problema resuelve.

No debe convertirse en una explicación de cómo está programado.

## 1.1. Planteamiento del problema

### 1.1.1. Contexto industrial

Explica qué son los procesos por lotes, por qué necesitan coordinación procedimental y por qué las recetas y el seguimiento de la ejecución son importantes.

Puedes describir cómo un sistema de gestión batch relaciona la estrategia de producción con el modelo físico de planta y el controlador.

### 1.1.2. Contexto tecnológico

Introduce brevemente el papel de los sistemas de automatización industrial, PLC4X y Malbec.

Explica qué proporciona la plataforma sobre la que se desarrolla el módulo y qué capacidades de alto nivel se requieren para gestionar recetas y ejecuciones.

### 1.1.3. Estado inicial del proyecto

Este apartado debe describir la situación que existía cuando comenzó tu trabajo, en la medida en que puedas reconstruirla y documentarla.

Explica:

- Qué funcionalidades de Malbec estaban disponibles.
- Qué soporte existía para el proyecto y sus módulos.
- Qué componentes de ISA-88 estaban implementados, si ya existían.
- Qué capacidades faltaban para configurar los modelos físico y procedimental.
- Qué limitaciones motivaron el desarrollo del módulo.

Distingue lo comprobado en el código de lo que provenga de entrevistas, observaciones o documentos de la empresa.

### 1.1.4. Problema específico que se aborda

Formula el problema central: la necesidad de incorporar capacidades para modelar una planta industrial, configurar recetas batch y relacionarlas con la infraestructura de control para facilitar su ejecución, supervisión y trazabilidad.

Actualiza esta formulación con el alcance real que hayas acordado con tu tutor y la empresa.

## 1.2. Formulación del problema

Puedes conservar las preguntas que aparecen en el anteproyecto, refinándolas cuando sea necesario:

1. ¿Qué requerimientos técnicos y jerarquías de ISA-88 se aplican al modelado de los procesos por lotes?
2. ¿Qué requisitos funcionales y no funcionales debe satisfacer el sistema?
3. ¿Cómo deben diseñarse los componentes necesarios para modelar la planta, definir las recetas y comunicar el sistema con el entorno industrial?
4. ¿Cómo se integran los módulos de configuración, comunicación y runtime?
5. ¿Cómo se puede validar la funcionalidad y el comportamiento del sistema en un entorno controlado?

Estas preguntas se corresponden con la dirección de investigación ya definida en el anteproyecto. Anteproyecto Daniel Campos

## 1.3. Objetivos

### 1.3.1. Objetivo general

Utiliza el objetivo general aprobado en el anteproyecto, salvo que tengas autorización para modificarlo.

### 1.3.2. Objetivos específicos

Mantén los cinco objetivos específicos aprobados y vincula cada uno con resultados concretos de tu implementación.

No es necesario crear nuevos objetivos simplemente porque el proyecto se haya vuelto más complejo. Puedes detallar las actividades necesarias para alcanzarlos sin cambiar su intención original.

## 1.4. Justificación

Organiza la justificación en cuatro dimensiones.

**Tecnológica:** integración entre la configuración de recetas y el entorno industrial.

**Económica:** contribución al desarrollo de una herramienta abierta, en relación con las limitaciones económicas de las soluciones propietarias descritas en el anteproyecto.

**Práctica:** utilidad para ingenieros de automatización y operadores que necesitan definir y supervisar procesos batch.

**Académica:** aplicación de conocimientos de ingeniería de software, modelado de dominio, arquitectura modular, pruebas e integración de sistemas.

## 1.5. Alcance y delimitaciones

Esta sección debe ser especialmente precisa.

Incluye los componentes que forman parte del sistema propuesto y de la implementación final: modelo físico, editor de recetas, comunicación, runtime, supervisión y trazabilidad, según corresponda.

También debe identificar los límites del trabajo. Por ejemplo, no es lo mismo demostrar un flujo completo mediante simulación que validar un proceso industrial con un PLC real.

Si implementas ambas alternativas, explica qué valida cada una y cuáles son las limitaciones de la demostración.

## 1.6. Limitaciones y supuestos

Documenta aspectos como la disponibilidad del controlador, el acceso al servidor EPICS, las señales disponibles, la configuración de la instalación y las condiciones de las pruebas.

En particular, deja claro si todas las operaciones del sistema se validaron con hardware real o si parte de ellas se probaron mediante simulación.

---

# CAPÍTULO II. MARCO TEÓRICO

El objetivo de este capítulo es ofrecer los conceptos necesarios para comprender el diseño. No debe ser una recopilación extensa de definiciones que nunca vuelvan a aparecer en los capítulos de implementación.

Cada tema debe ayudar a justificar una decisión, explicar una estructura de datos o entender un requisito.

## 2.1. Antecedentes

Documenta trabajos y herramientas relacionadas con los siguientes aspectos:

- Sistemas de control batch y modelado de recetas.
- Aplicaciones industriales que utilizan ISA-88.
- Herramientas abiertas de automatización industrial.
- Editores gráficos de procesos secuenciales.
- Integración entre aplicaciones de alto nivel y sistemas de control.

Para cada antecedente, explica su objetivo, el enfoque utilizado, los resultados y su relación concreta con Malbec.

Una tabla comparativa puede ayudarte a evitar descripciones demasiado genéricas.

## 2.2. Automatización industrial y procesos batch

### 2.2.1. Automatización industrial y relación IT/OT

Explica la diferencia entre el software que gestiona la información del proceso y los componentes que realizan el control físico.

### 2.2.2. Procesos continuos, discretos y por lotes

Justifica por qué el proyecto se centra en la producción batch.

### 2.2.3. Sistemas de gestión batch

Introduce las funciones de configuración, coordinación, supervisión y trazabilidad que se esperan de un sistema de este tipo.

## 2.3. Estándar ISA-88

Esta será una de las secciones centrales del marco teórico.

### 2.3.1. Fundamentos y terminología.

### 2.3.2. Modelo físico.

Explica los niveles de organización de la planta y relaciónalos con las clases utilizadas en `malbec-s88`.

### 2.3.3. Modelo de control procedimental.

Explica la estructura jerárquica de los procedimientos, los procedimientos de unidad, las operaciones y las fases.

### 2.3.4. Modelo de recetas.

Explica los tipos de receta contemplados y la diferencia entre la definición maestra y la receta de control utilizada en una ejecución particular.

### 2.3.5. Requisitos y restricciones que se derivan de ISA-88.

Aquí debes empezar a conectar la teoría con las validaciones que implementaste: niveles jerárquicos, relaciones entre elementos, parámetros, asociaciones y restricciones de la estructura procedimental.

El anteproyecto ya fundamenta el trabajo en estos modelos físico, procedimental y de recetas. Anteproyecto Daniel Campos

## 2.4. Diagramas de funciones secuenciales (SFC)

### 2.4.1. Elementos de un SFC.

### 2.4.2. Pasos, transiciones y conexiones.

### 2.4.3. Bifurcaciones y sincronizaciones.

### 2.4.4. Representación gráfica frente a semántica de ejecución.

Esta última distinción es importante para tu implementación.

Explica que la estructura procedimental define las relaciones entre pasos y transiciones; el motor de disposición gráfica calcula cómo dibujarlos, mientras que el runtime requiere información adicional sobre el estado del proceso.

## 2.5. Representación e intercambio de datos

### 2.5.1. XML como formato de representación.

### 2.5.2. B2MML y BatchML.

### 2.5.3. Repositorios y adaptadores de almacenamiento.

### 2.5.4. Validación y conservación de la información.

Debes concentrarte en los formatos realmente utilizados y en las razones por las que se eligieron. El anteproyecto ya contempla B2MML y BatchML para representar los modelos correspondientes. Anteproyecto Daniel Campos

## 2.6. Tecnologías del proyecto

Incluye únicamente aquellas que resulten pertinentes para comprender la solución:

- Java.
- Apache Maven.
- NetBeans Platform.
- NetBeans Visual Library.
- Apache PLC4X.
- EPICS, en relación con la integración real del runtime.
- Herramientas y bibliotecas de pruebas empleadas.

Explica para qué utilizaste cada tecnología, no solamente qué es.

## 2.7. Arquitectura de software y prácticas de desarrollo

### 2.7.1. Arquitectura en capas.

### 2.7.2. Principios de Clean Architecture.

### 2.7.3. Separación del dominio y los casos de uso.

### 2.7.4. Abstracción de dependencias mediante interfaces.

### 2.7.5. Pruebas unitarias e integración.

### 2.7.6. Desarrollo guiado por pruebas (TDD).

### 2.7.7. UML como herramienta de análisis y diseño.

### 2.7.8. Proceso Unificado Racional (RUP).

Esta última subsección deberá explicar las fases y entregables de RUP, para después relacionarlos con el trabajo que realmente realizaste.

## 2.8. Supervisión y trazabilidad de ejecución

Explica los conceptos que utilizarás para registrar y mostrar la ejecución:

- Estados de receta y estados de paso.
- Eventos de proceso.
- Marcas temporales.
- Logs de ejecución.
- Reportes de lote.
- Errores de comunicación y recuperación.

## 2.9. Glosario técnico

Puedes dejar un glosario general en esta sección o incorporarlo después en el capítulo de requisitos.

Lo importante es establecer un vocabulario consistente para términos como receta maestra, receta de control, fase, paso, transición, sesión de ejecución, equipo, variable de proceso y evento de runtime.

---

# CAPÍTULO III. MARCO METODOLÓGICO

Este capítulo responde a una pregunta distinta de la arquitectura:

**¿Cómo se llevó a cabo la investigación y el desarrollo, qué información se utilizó y cómo se verificaron los resultados?**

La tesis de ejemplo utiliza un capítulo independiente para el marco metodológico y dedica otro al desarrollo y los resultados. Te recomiendo conservar esa separación. TESIS

## 3.1. Tipo de investigación

Describe el trabajo como un proyecto orientado a resolver un problema tecnológico concreto mediante el diseño, la construcción y la validación de software.

La denominación académica precisa debe corresponder con las categorías admitidas por tu universidad. No es conveniente afirmar un tipo de investigación únicamente porque aparezca en otra tesis.

## 3.2. Nivel de investigación

Explica hasta dónde llega el estudio: desde el análisis del problema hasta la construcción de una solución y su evaluación en un entorno controlado.

Si utilizas términos como descriptivo, aplicado o evaluativo, justifica por qué corresponden a las actividades que realmente realizaste.

## 3.3. Técnicas de recolección de información

El anteproyecto contempla la revisión documental, la observación directa y la interacción con los ingenieros de la empresa. Anteproyecto Daniel Campos

Para el trabajo final, documenta cuáles de esas técnicas utilizaste efectivamente y cómo.

### 3.3.1. Revisión documental

Describe el análisis de ISA-88, la documentación del proyecto Malbec, los formatos utilizados y los documentos técnicos relevantes.

### 3.3.2. Observación del sistema y del entorno industrial

Explica qué funcionalidades observaste, qué comportamiento necesitabas reproducir y qué restricciones encontraste.

### 3.3.3. Consultas a los responsables técnicos

Si mantuviste reuniones, entrevistas o sesiones de revisión con los ingenieros, documenta qué información obtuviste y cómo influyó en los requisitos.

No incluyas técnicas que no hayas utilizado realmente.

## 3.4. Instrumentos y herramientas

Puedes presentar los instrumentos utilizados para organizar el trabajo: documentos de requisitos, tablas de trazabilidad, plantillas de casos de uso, diagramas UML, casos de prueba y registros de ejecución.

También identifica el entorno técnico empleado: versiones del JDK, Maven, NetBeans Platform y bibliotecas pertinentes, una vez verificadas.

## 3.5. Metodología de desarrollo

Aquí debes resolver la cuestión de RUP.

El anteproyecto establece explícitamente cuatro fases: inicio, elaboración, construcción y transición. También relaciona cada fase con actividades concretas, como la especificación de requisitos, el diseño arquitectónico, la implementación de los módulos, las pruebas y la documentación. Anteproyecto Daniel Campos

**No te recomiendo afirmar que aplicaste RUP estrictamente si ese no fue tu proceso real de desarrollo.** Puedes organizar la memoria de acuerdo con esas cuatro fases, pero debes explicar con honestidad cómo las utilizaste.

Una formulación defendible, si refleja lo sucedido, sería:

> El desarrollo se documenta tomando como referencia las fases de RUP establecidas en el anteproyecto. La implementación se realizó mediante un enfoque iterativo e incremental, incorporando separación de responsabilidades, arquitectura modular, casos de uso y pruebas automatizadas. La aplicación de las fases de RUP fue adaptada a las características del proyecto y a las necesidades del entorno de desarrollo.

No presentes como contemporáneos artefactos que hayas elaborado después del desarrollo. Si reconstruyes ahora los diagramas de análisis o diseño a partir del código, puedes identificarlos como modelos elaborados durante la documentación técnica del sistema implementado.

Esto no debilita la tesis. Al contrario: permite explicar las decisiones arquitectónicas con evidencia real y sin fingir un proceso que no ocurrió.

## 3.6. Estrategia de pruebas y validación

Explica la diferencia entre:

- Pruebas unitarias de las clases del dominio y los casos de uso.
- Pruebas de integración entre módulos.
- Pruebas de persistencia y recuperación de información.
- Pruebas de caja negra desde las interfaces de usuario.
- Pruebas de ejecución en el simulador.
- Pruebas con EPICS y con el PLC real, si se realizan.
- Pruebas de aceptación de los escenarios principales.

Define qué significa que una prueba sea satisfactoria y qué evidencia utilizarás para demostrar el resultado.

## 3.7. Entorno de pruebas

Describe el equipo de desarrollo, las dependencias necesarias, el entorno NetBeans, las herramientas de pruebas y la infraestructura de comunicación disponible.

Si utilizas un simulador, especifica qué componentes simula y cuáles siguen siendo reales.

## 3.8. Plan de trabajo

Expón la secuencia de actividades y sus dependencias.

El cronograma del anteproyecto debe revisarse para reflejar el estado real del proyecto y las actividades pendientes. No conviene conservar fechas o duraciones que ya no correspondan al plan vigente.

---

# CAPÍTULO IV. RESULTADOS

Este debe ser el capítulo más extenso de la tesis. Aquí demuestras qué analizaste, diseñaste, implementaste y verificaste.

Mi recomendación es organizarlo siguiendo las fases de RUP que aparecen en el anteproyecto, pero utilizando una numeración consistente y adaptada al trabajo real.

El ejemplo de tesis sigue una organización semejante: primero requisitos y riesgos; después casos de uso, análisis, diseño, arquitectura, implementación y pruebas. TESIS

## 4.1. Fase de inicio: diagnóstico y requisitos

### 4.1.1. Contexto del sistema

Describe dónde se encuentra Malbec dentro del ecosistema tecnológico y cuáles son sus límites.

Incluye un diagrama de contexto que identifique el sistema, los usuarios y las dependencias externas relevantes.

### 4.1.2. Estado inicial del proyecto

Documenta lo encontrado antes de desarrollar `malbec-s88`.

Distingue las capacidades preexistentes de las funcionalidades que se incorporarían.

### 4.1.3. Diagnóstico de la arquitectura y los módulos existentes

Explica cómo estaba organizado el proyecto y qué restricciones imponían su tecnología, modularidad y plataforma.

Aquí puedes introducir la estructura actual de `malbec-s88` y describir el papel de los módulos relevantes.

### 4.1.4. Modelo de dominio

Presenta los conceptos fundamentales del sistema y las relaciones entre ellos.

No debe ser todavía un inventario de todas las clases Java. Primero explica los conceptos industriales: planta, equipo, receta, procedimiento, paso, transición, parámetro y ejecución.

Después relaciona estos conceptos con las estructuras implementadas.

### 4.1.5. Glosario de términos

Define los términos técnicos que aparecerán a lo largo de la memoria.

### 4.1.6. Requisitos funcionales

Agrúpalos por módulo.

Por ejemplo:

| Identificador ilustrativo | Requisito |
|---|---|
| RF-01 | Permitir la creación y modificación de la jerarquía física de la planta. |
| RF-02 | Permitir la configuración de las propiedades de los elementos físicos. |
| RF-03 | Guardar y cargar el modelo físico. |
| RF-04 | Permitir la creación y modificación de recetas. |
| RF-05 | Permitir la construcción de procedimientos mediante pasos y transiciones. |
| RF-06 | Permitir la asociación entre pasos de receta y equipos compatibles. |
| RF-07 | Permitir la configuración y validación de parámetros. |
| RF-08 | Validar la estructura procedimental antes de la ejecución. |
| RF-09 | Permitir la preparación de una receta de control para un lote concreto. |
| RF-10 | Permitir el inicio y la supervisión de una ejecución, según las capacidades implementadas. |
| RF-11 | Mostrar los cambios de estado del proceso en el diagrama SFC. |
| RF-12 | Registrar los eventos relevantes de la ejecución. |
| RF-13 | Consultar o generar un reporte de ejecución. |
| RF-14 | Permitir las pruebas del flujo de ejecución en un entorno simulado, si se implementa esa funcionalidad. |

Estos identificadores son una propuesta inicial. Debes ajustarlos a los requisitos aprobados y a las funcionalidades finales.

Para cada requisito, registra su prioridad, origen, módulo responsable y método de verificación.

### 4.1.7. Requisitos no funcionales

Incluye, como mínimo, las categorías que realmente condicionen el proyecto:

- Modularidad y mantenibilidad.
- Integridad de los datos.
- Consistencia del modelo.
- Robustez ante errores de comunicación.
- Capacidad de prueba.
- Usabilidad de las interfaces.
- Compatibilidad con el entorno de ejecución.
- Trazabilidad.
- Seguridad operacional, especialmente en la interacción con el controlador.

No basta con enumerarlos. Debes especificar cómo se comprobará cada requisito cuando sea verificable mediante una prueba.

### 4.1.8. Identificación y mitigación de riesgos

Esta sección debe basarse en el estado inicial, el entorno industrial y las decisiones técnicas del proyecto.

La tesis de ejemplo incorpora una identificación de riesgos, sus niveles de impacto y las medidas de contingencia. Es un esquema útil para adaptar a tu caso. TESIS

Mi propuesta inicial es la siguiente:

| Riesgo | Impacto | Mitigación propuesta |
|---|---|---|
| Disponibilidad limitada del servidor EPICS o del PLC | Alto | Implementar un simulador y establecer una interfaz de comunicación desacoplada. |
| Interpretación incorrecta de los modelos ISA-88 | Alto | Validar el modelo de dominio con los ingenieros y crear pruebas para sus invariantes. |
| Asociación incorrecta entre recetas y equipos | Alto | Validar las referencias y compatibilidad antes de iniciar la ejecución. |
| Incompatibilidades de serialización | Alto | Implementar pruebas de lectura, escritura y recuperación del modelo. |
| Errores en estructuras SFC complejas | Alto | Crear casos de prueba para bifurcaciones, sincronizaciones, ciclos y conexiones inválidas. |
| Ejecución accidental de comandos en la instalación real | Crítico | Separar el entorno simulado del real y exigir las verificaciones y autorizaciones que defina el entorno industrial. |
| Pérdida o desorden de eventos de runtime | Alto | Definir marcas temporales, manejo de errores y reglas de actualización del estado. |
| Retrasos por intentar completar demasiadas funcionalidades | Alto | Priorizar el alcance aprobado, cerrar los componentes esenciales y dejar las mejoras adicionales para recomendaciones. |

Esta tabla debe perfeccionarse con la probabilidad, el impacto, la prioridad, el responsable y el estado de cada riesgo.

### 4.1.9. Identificación de actores

Como mínimo, contempla los perfiles pertinentes para el alcance aprobado.

**Ingeniero de procesos o automatización:** configura el modelo físico y define las recetas.

**Operador:** selecciona una receta de control, solicita operaciones permitidas y supervisa la ejecución.

**Sistema externo de comunicación:** participa en el intercambio de comandos y datos.

**Responsable técnico o administrador:** solo debe ser un actor independiente si realmente existen funciones diferenciadas que lo justifiquen.

### 4.1.10. Modelo de casos de uso

Prepara un diagrama general y describe los casos de uso relevantes.

Una lista inicial puede ser:

1. Crear un proyecto de planta.
2. Configurar la jerarquía física.
3. Configurar propiedades y variables.
4. Crear un conjunto de recetas.
5. Crear una receta.
6. Editar el procedimiento mediante el editor SFC.
7. Asociar pasos con equipos.
8. Configurar parámetros.
9. Validar una receta.
10. Guardar y cargar una receta.
11. Preparar una receta de control.
12. Iniciar una ejecución controlada.
13. Supervisar el estado del proceso.
14. Consultar logs.
15. Generar un reporte.
16. Simular una ejecución, si se implementa esa funcionalidad.

No necesitas incluir todos los casos de uso en el diagrama principal. Puedes agruparlos o establecer diagramas secundarios para mantener la legibilidad.

### 4.1.11. Descripción detallada de los casos de uso

Para cada caso de uso importante, especifica:

- Identificador y nombre.
- Objetivo.
- Actor principal.
- Actores secundarios.
- Precondiciones.
- Flujo normal.
- Flujos alternativos.
- Errores posibles.
- Postcondiciones.
- Requisitos relacionados.
- Casos de prueba asociados.

Te recomiendo documentar detalladamente todos los casos de uso principales, y resumir los auxiliares.

---

## 4.2. Fase de elaboración: análisis y diseño

En esta fase debes responder cómo se organizará la solución para satisfacer los requisitos identificados.

### 4.2.1. Modelo de análisis del dominio

Presenta las clases conceptuales y las relaciones entre ellas.

Distingue las entidades del dominio de las interfaces gráficas, los servicios de aplicación y los adaptadores de infraestructura.

En la medida en que la arquitectura del proyecto lo permita, utiliza la distinción entre clases de entidad, interfaz y control para analizar casos de uso. Puedes relacionarlo con los estereotipos de análisis utilizados en la tesis de ejemplo. TESIS

### 4.2.2. Diagramas de colaboración e interacción

Selecciona los casos de uso más representativos para explicar la comunicación entre los componentes.

Por ejemplo:

- Crear un elemento físico.
- Crear y guardar una receta.
- Asociar un paso con un equipo.
- Preparar una receta de control.
- Iniciar una ejecución.
- Recibir una actualización del runtime y reflejarla en el diagrama.

Para cada interacción debes poder explicar qué componente inicia la operación, qué componentes participan y dónde se aplican las reglas.

### 4.2.3. Arquitectura candidata y decisiones arquitectónicas

Esta sección es una de las más importantes de todo el documento.

Debe explicar la arquitectura que encontraste y las decisiones que tomaste para desarrollar el sistema.

Incluye:

1. El estilo arquitectónico utilizado y por qué.
2. La división en módulos.
3. Las responsabilidades de `api`, `core`, `data`, `plant`, `recipes` y `project`.
4. El papel que tendrá `batch`.
5. La dirección de las dependencias.
6. La comunicación entre la interfaz, los casos de uso y la persistencia.
7. La forma en que se aislará la comunicación con EPICS.
8. Las decisiones para facilitar las pruebas unitarias.
9. Las limitaciones arquitectónicas observadas y las mejoras realizadas, si las hubo.

No necesitas presentar una arquitectura idealizada. Debes representar lo que realmente existe y justificar los cambios que propongas.

### 4.2.4. Diagrama de componentes y dependencias

Este diagrama debe mostrar los módulos Maven, sus responsabilidades y las relaciones entre ellos.

La arquitectura inicial puede representar las dependencias actuales. Si incorporas componentes nuevos para runtime, utiliza un segundo diagrama que muestre la arquitectura final o propuesta.

Es importante distinguir ambos estados para no confundir lo implementado con lo diseñado.

### 4.2.5. Diagrama de despliegue

Muestra los elementos físicos o lógicos que participan en la ejecución.

Dependiendo de la implementación final, puede incluir:

- Aplicación Malbec ejecutándose sobre NetBeans Platform.
- Archivos del proyecto y modelos persistidos.
- Servidor EPICS.
- PLC industrial.
- Sistema de simulación.
- Componentes adicionales de comunicación que formen parte de la instalación.

El diagrama debe reflejar la topología real y no presuponer un mecanismo de comunicación que no hayas utilizado.

### 4.2.6. Modelo de datos y persistencia

Explica cómo se representan y almacenan las entidades del sistema.

Incluye los formatos y mecanismos reales de almacenamiento, sus interfaces y las clases responsables de la lectura y escritura.

Si los datos se almacenan en archivos XML, no fuerces un diagrama entidad-relación propio de una base de datos relacional. Puedes utilizar un modelo de clases, un esquema de los documentos XML o un diagrama de componentes que represente los proveedores de repositorio.

### 4.2.7. Diseño de las interfaces

Presenta las pantallas principales y el flujo de navegación.

Aquí debes mostrar cómo el usuario interactúa con el editor físico y el editor de recetas, y cómo se relaciona la edición con los modelos subyacentes.

Para la interfaz de runtime, utiliza prototipos solamente cuando correspondan a funcionalidades que hayas implementado o que estén claramente identificadas como diseño pendiente.

---

## 4.3. Fase de construcción: implementación de los módulos

Esta sección debe explicar cómo se materializó el diseño.

No la organices únicamente por archivos Java. Conviene hacerlo por responsabilidades funcionales para que sea legible sin conocer de antemano el repositorio.

### 4.3.1. Implementación del modelo de dominio

Explica cómo se representan la planta y las recetas en el modelo de datos.

Selecciona las clases cruciales y justifica sus responsabilidades.

Por ejemplo:

- `S88Element`.
- `S88PlantModel`.
- `S88PlantSnapshot`.
- `S88Recipe`.
- `S88MasterRecipe`.
- `S88ControlRecipe`.
- `S88RecipeElement`.
- `S88ProcedureLogic`.
- `S88ProcedureStep`.
- `S88ProcedureTransition`.
- `S88ProcedureLink`.

No describas todas las clases con el mismo nivel de detalle. Prioriza las que permitan entender las relaciones entre los conceptos y las operaciones que se ejecutan sobre ellos.

### 4.3.2. Implementación de la lógica de aplicación

Explica cómo se realizan las acciones que modifican el modelo.

Puedes utilizar `CreateElementUseCase`, `CreateMasterRecipeUseCase`, `BindRecipeStepUseCase` y `EditProcedureLogicUseCase` como ejemplos, siempre que reflejen responsabilidades relevantes para tu contribución.

Para cada caso seleccionado:

1. Explica qué operación resuelve.
2. Identifica sus entradas y salidas.
3. Describe las validaciones realizadas.
4. Explica cómo se modifica el modelo.
5. Muestra qué pruebas verifican el comportamiento.
6. Justifica por qué esa lógica pertenece al caso de uso y no a la interfaz.

### 4.3.3. Persistencia y gestión de archivos

Explica cómo se guardan y cargan los modelos y cómo se separa el almacenamiento de las reglas del dominio.

Documenta los repositorios y proveedores reales. Incluye un flujo que muestre qué sucede cuando el usuario guarda una receta, qué componente realiza la serialización y cómo se reconstruye el modelo cuando vuelve a abrirla.

### 4.3.4. Editor del modelo físico

Esta sección puede convertirse en uno de los apartados más sólidos de la implementación, porque indicas que ya está terminado.

Debe incluir:

- Función del editor.
- Estructura jerárquica de la planta.
- Creación, modificación y eliminación de elementos.
- Gestión de propiedades y parámetros.
- Integración con las clases del dominio.
- Persistencia del modelo.
- Gestión de errores.
- Interacción con otros módulos.
- Evidencias de las pruebas realizadas.

Incluye capturas de pantalla de las funcionalidades más relevantes, acompañadas de una explicación técnica de lo que sucede en el código.

### 4.3.5. Editor de recetas y representación SFC

Este apartado merece un desarrollo extenso.

Explica cómo se representa la estructura procedimental y cómo la interfaz permite modificarla.

Recomiendo dividirlo en los siguientes puntos:

**a. Modelo de edición.** Explica cómo `RecipeEditorModel` mantiene la receta abierta, gestiona los cambios y coordina las operaciones de edición y guardado.

**b. Vista gráfica y vista tabular.** Describe las responsabilidades de `RecipeChartView`, `RecipeChartScene` y `RecipeTableView`, en la medida en que estén implementadas.

**c. Disposición gráfica.** Explica `SfcLayoutEngine`, el cálculo de posiciones, la distribución de pasos y bifurcaciones y la relación con las dimensiones de las figuras.

**d. Dibujo y conexiones.** Describe el papel de los componentes gráficos, anclajes y mecanismos de enrutamiento.

**e. Edición de la estructura.** Explica cómo las acciones de usuario terminan modificando el modelo de receta mediante las operaciones de aplicación.

**f. Asociación con equipos.** Documenta cómo se vincula cada paso con el equipo pertinente y qué validaciones se realizan.

**g. Parámetros y condiciones.** Explica cómo se representan, modifican y validan.

**h. Persistencia y pruebas.** Describe cómo se conserva la receta y cómo verificaste su comportamiento.

No conviertas esta sección en un comentario línea por línea de todas las clases. El propósito es explicar las relaciones entre el modelo, los casos de uso, el editor y el gráfico resultante.

### 4.3.6. Implementación del módulo de comunicación

Este apartado deberá ajustarse al mecanismo de comunicación real.

Describe la arquitectura de comunicación, las interfaces y el adaptador utilizado, incluyendo el intercambio de comandos y datos con EPICS.

Como mínimo, debería explicar:

1. Establecimiento y cierre de la conexión.
2. Identificación y configuración de las variables.
3. Lectura de estados y parámetros.
4. Escritura de los comandos autorizados.
5. Confirmación de los resultados de las operaciones.
6. Tratamiento de excepciones y tiempos de espera.
7. Recuperación y presentación de errores.
8. Registro de los problemas de comunicación.

Si utilizas interfaces de aplicación, muestra cómo el núcleo solicita la operación sin depender de la implementación específica del transporte.

### 4.3.7. Implementación del runtime de la receta

Esta sección debe diferenciar la receta definida de su ejecución concreta.

Explica el estado inicial, los cambios de estado, la información recibida y los datos que utiliza la vista de supervisión.

Incluye:

- Preparación de la receta de control.
- Verificaciones previas a la ejecución.
- Creación de la sesión de ejecución.
- Solicitud de los comandos permitidos.
- Recepción y normalización de eventos.
- Actualización del estado de la receta y sus pasos.
- Finalización y registro de resultados.
- Gestión de errores.

Debes ser especialmente explícito acerca de qué comportamiento corresponde a Malbec y cuál corresponde al PLC.

Si el PLC es el responsable de ejecutar la secuencia, no atribuyas al runtime de Malbec una lógica de control que realmente reside en el controlador.

### 4.3.8. Simulación de ejecución

Si implementas el simulador, explica su objetivo, su modelo de estados, los eventos que genera y las limitaciones de la simulación.

Describe cómo puede utilizarse el mismo contrato de comunicación con el simulador y con el adaptador real.

También debes indicar qué no puede demostrar el simulador: por ejemplo, el comportamiento físico de los equipos o la fiabilidad de una comunicación real.

### 4.3.9. Logs y reportes

Describe la estructura de los registros de ejecución, los datos que se almacenan y la forma de consultarlos.

Puedes documentar un evento de ejemplo, siempre que corresponda al formato real.

Para el reporte, explica cómo se obtienen los datos, qué campos incluye y cómo se evita presentar información como confirmada cuando no se ha recibido o verificado.

### 4.3.10. Integración entre los módulos

Presenta un recorrido de extremo a extremo.

Por ejemplo: creación de una receta, validación, asociación con equipos, preparación de la ejecución, envío de comandos, recepción de estados y representación visual.

Un diagrama de secuencia puede resumir el flujo y servir como hilo conductor para la explicación de los distintos componentes.

### 4.3.11. Decisiones y dificultades de implementación

No escondas los problemas resueltos durante el desarrollo.

Puedes explicar decisiones relevantes sobre:

- Separación del modelo y la presentación.
- Representación de las estructuras procedimentales.
- Organización del layout SFC.
- Persistencia e interoperabilidad.
- Diseño de los contratos de comunicación.
- Integración con NetBeans Platform.
- Estrategia de pruebas.
- Gestión de errores.

Una buena sección de decisiones arquitectónicas debería responder tres preguntas: qué problema existía, qué alternativas consideraste y por qué elegiste la solución aplicada.

### 4.3.12. Pruebas unitarias desarrolladas durante la construcción

Explica cómo verificaste los comportamientos del modelo y los casos de uso.

Distingue las pruebas existentes en la instantánea del repositorio de las pruebas nuevas o modificadas que formen parte de tu contribución.

Si aplicaste TDD en determinadas funcionalidades, explica el proceso y aporta ejemplos verificables. No afirmes que todas las clases se desarrollaron mediante TDD si no fue así.

---

## 4.4. Fase de transición: validación del sistema

Esta sección debe demostrar que el sistema satisface los requisitos definidos.

No basta con colocar capturas de pantalla de la aplicación funcionando. Necesitas relacionar cada requisito importante con las pruebas que lo verifican.

### 4.4.1. Entorno de validación

Describe el entorno de pruebas y sus componentes. Especifica si la validación se realizó con archivos de proyecto, un simulador, un servidor EPICS o un PLC real.

### 4.4.2. Pruebas unitarias

Organiza las pruebas por módulos y responsabilidades.

Puedes incluir:

- Modelo físico.
- Reglas de conformidad.
- Creación y modificación de recetas.
- Parámetros.
- Enlaces y transiciones.
- Serialización y recuperación.
- Motor de disposición gráfica.
- Modelo de runtime.
- Adaptadores de comunicación.
- Generación de registros.

### 4.4.3. Pruebas de integración

Verifica la interacción entre componentes.

Ejemplos:

- Caso de uso y modelo.
- Modelo y persistencia.
- Editor y caso de uso.
- Receta guardada y receta recuperada.
- Runtime y adaptador de simulación.
- Adaptador de comunicación y procesamiento de eventos.
- Estado de ejecución y actualización del diagrama.

### 4.4.4. Pruebas de caja negra

Comprueba el comportamiento del sistema desde la perspectiva del usuario y del contrato funcional, sin depender de los detalles internos de implementación.

Algunos casos representativos:

| Caso de prueba | Acción | Resultado esperado |
|---|---|---|
| Creación de planta | Crear un proyecto y añadir elementos físicos. | La jerarquía queda representada y puede guardarse. |
| Edición de receta | Añadir pasos, transiciones y parámetros válidos. | La receta refleja la estructura definida por el usuario. |
| Reapertura | Guardar y volver a abrir una receta. | Se conserva la información que se guardó. |
| Asociación inválida | Intentar vincular un paso con un equipo no compatible. | El sistema rechaza la operación o informa de la incompatibilidad según el comportamiento especificado. |
| Entrada de parámetros inválida | Introducir un valor que no cumple las restricciones. | El sistema presenta un error y evita aceptar el dato inválido cuando así lo establezca el requisito. |
| Ejecución simulada | Iniciar una receta de prueba y producir cambios de estado. | La vista refleja los eventos simulados y registra los cambios correspondientes. |
| Pérdida de comunicación | Interrumpir la comunicación de prueba. | El sistema detecta y presenta el problema sin ocultar la incertidumbre sobre el estado real. |
| Consulta de ejecución | Solicitar un reporte de una ejecución finalizada. | El reporte contiene los eventos registrados para esa sesión. |

Los resultados deben reflejar las pruebas que efectivamente hayas ejecutado. Si alguna funcionalidad permanece sin implementar, identifícala como pendiente y no la presentes como validada.

### 4.4.5. Pruebas de robustez y escenarios de error

Incluye los errores que puedan comprometer la integridad del sistema:

- Datos faltantes o inválidos.
- Estructuras de receta incorrectas.
- Referencias a equipos inexistentes.
- Archivos inválidos.
- Valores de proceso no disponibles.
- Tiempos de espera.
- Pérdida de comunicación.
- Eventos repetidos o fuera de orden.
- Errores durante la generación de reportes.

### 4.4.6. Resultados y evidencias

Cada prueba documentada debe contener:

- Identificador.
- Requisito asociado.
- Precondiciones.
- Datos de entrada.
- Procedimiento.
- Resultado esperado.
- Resultado obtenido.
- Estado final: aprobado o fallido.
- Evidencia.
- Observaciones.

Las pruebas que no se ejecutaron deben figurar como pendientes, no como satisfactorias.

### 4.4.7. Matriz de trazabilidad

Esta matriz es una de las mejores maneras de demostrar que el sistema responde a los objetivos académicos y técnicos.

Relaciona:

**Objetivo específico → requisito → caso de uso → componente o módulo → caso de prueba → resultado.**

Por ejemplo:

| Objetivo | Resultado técnico | Evidencia |
|---|---|---|
| Identificar los requisitos ISA-88 | Glosario, modelo de dominio y requisitos documentados. | Tablas, diagramas y referencias. |
| Identificar requisitos del sistema | Requisitos funcionales, no funcionales y riesgos. | Especificación de requisitos. |
| Diseñar los módulos | Arquitectura, modelos de análisis y diseño. | Diagramas y justificación de decisiones. |
| Codificar los módulos | Componentes implementados e integrados. | Código y pruebas asociadas. |
| Validar el funcionamiento | Escenarios reproducibles y resultados documentados. | Informes de pruebas, logs y capturas. |

La relación debe mostrar qué se consiguió y con qué evidencia.

---

# CAPÍTULO V. CONCLUSIONES Y RECOMENDACIONES

Este capítulo debe cerrar el trabajo respondiendo a los objetivos, explicando los resultados y delimitando las mejoras futuras.

## 5.1. Conclusiones

Te recomiendo organizar las conclusiones según los cinco objetivos específicos del anteproyecto.

### Conclusión 1: requisitos ISA-88

Resume qué requisitos y jerarquías fueron identificados y cómo influyeron en el modelo de dominio.

### Conclusión 2: requisitos funcionales y no funcionales

Explica cómo se identificaron, organizaron y utilizaron para definir las capacidades del sistema.

### Conclusión 3: diseño de los módulos

Resume las decisiones arquitectónicas fundamentales, cómo se dividieron las responsabilidades y por qué se adoptó ese diseño.

### Conclusión 4: implementación e integración

Describe qué módulos se construyeron, cómo se integraron y cuáles fueron los resultados técnicos alcanzados.

### Conclusión 5: validación

Presenta los resultados obtenidos mediante las pruebas y especifica las capacidades que fueron demostradas.

Para cada objetivo indica si se alcanzó completamente, parcialmente o si quedó pendiente.

No bases una conclusión solamente en que una funcionalidad existe en el código. Debes considerar también su integración y el nivel de validación que alcanzaste.

## 5.2. Recomendaciones

Aquí deben aparecer las mejoras que serían valiosas para un sistema de este tipo, pero que no forman parte de la implementación validada del trabajo de grado.

No es una lista de funcionalidades que olvidaste programar. Son líneas de evolución que pueden plantearse a partir de las limitaciones y resultados del proyecto.

Recomiendo organizar las recomendaciones por áreas.

### 5.2.1. Evolución del modelo ISA-88

- Ampliar la cobertura de elementos y restricciones del estándar.
- Mejorar las herramientas de validación semántica.
- Incorporar verificaciones adicionales de consistencia entre recetas y equipos.

### 5.2.2. Evolución del runtime

- Ampliar la gestión de fallos y recuperación.
- Mejorar la supervisión de múltiples ejecuciones.
- Extender la compatibilidad con diferentes mecanismos de comunicación.
- Incorporar mecanismos de recuperación del estado cuando la aplicación se reinicia.

### 5.2.3. Trazabilidad y persistencia

- Desarrollar capacidades históricas más avanzadas.
- Mejorar la búsqueda y consulta de eventos.
- Ampliar los formatos de exportación.
- Incorporar versionado y migración de recetas.

### 5.2.4. Calidad y seguridad operacional

- Reforzar los mecanismos de autorización para las operaciones de control.
- Evaluar estrategias de redundancia y recuperación.
- Añadir pruebas con condiciones de comunicación más complejas.
- Establecer mecanismos adicionales de auditoría de las operaciones relevantes.

### 5.2.5. Usabilidad y documentación

- Ampliar los manuales.
- Mejorar mensajes y mecanismos de diagnóstico.
- Facilitar la creación y reutilización de recetas.
- Desarrollar herramientas adicionales de análisis y supervisión.

Prioriza las recomendaciones según su impacto y su relación con las limitaciones identificadas durante las pruebas.

---

# 5. Diagramas que deberías elaborar

Mi recomendación es que selecciones los diagramas según la pregunta que deben responder. No necesitas crear un diagrama de cada tipo UML simplemente para demostrar que conoces el lenguaje.

Esta sería la colección principal.

| N.º | Diagrama | Qué debe mostrar | Sección |
|---|---|---|---|
| 1 | Contexto del sistema | Usuarios, Malbec, infraestructura y sistemas externos relevantes. | 4.1 |
| 2 | Estado inicial del proyecto | Componentes existentes y capacidades que faltaban. | 4.1 |
| 3 | Modelo de dominio | Planta, recetas, procedimientos, pasos, transiciones y relaciones. | 4.1–4.2 |
| 4 | Casos de uso | Actores y funcionalidades principales. | 4.1 |
| 5 | Clases de análisis | Entidades, interfaces y responsabilidades de control asociadas a los casos de uso. | 4.2 |
| 6 | Componentes y dependencias | Módulos Maven y dependencias arquitectónicas. | 4.2 |
| 7 | Secuencia de creación de una receta | Interacciones entre interfaz, casos de uso, modelo y almacenamiento. | 4.2–4.3 |
| 8 | Secuencia de preparación y ejecución | Validación, envío de comandos, recepción de datos y actualización del runtime. | 4.2–4.3 |
| 9 | Flujo de información | Cómo se transforman y transportan los datos entre componentes. | 4.2–4.3 |
| 10 | Máquina de estados de ejecución | Estados de una sesión, transiciones permitidas y condiciones de cambio. | 4.2–4.3 |
| 11 | Despliegue | Aplicación, servidor de comunicación, PLC y simulador, si existe. | 4.2 |
| 12 | Modelo de persistencia | Repositorios, almacenamiento, serialización y recuperación de datos. | 4.2–4.3 |
| 13 | Flujo de edición SFC | Cómo las acciones de usuario cambian el modelo y se reflejan en el gráfico. | 4.3 |
| 14 | Arquitectura de pruebas | Componentes probados, dobles de prueba y límites de integración. | 4.4 |

Los diagramas 1, 3, 4, 6, 8, 10 y 11 son especialmente importantes. Con ellos puedes explicar la arquitectura general, el comportamiento y la integración del sistema sin dispersar demasiado la documentación.

## Dos diagramas que merecen una atención especial

### Diagrama de arquitectura por módulos

Debería mostrar claramente la separación actual entre `api`, `core`, `data`, `plant`, `recipes` y `project`, además de representar los componentes de comunicación y runtime cuando estén definidos.

Este diagrama debe ser coherente con las dependencias reales del proyecto.

### Diagrama de secuencia del runtime

Debería representar un flujo como el siguiente:

1. El operador solicita una operación.
2. La aplicación valida el estado y las condiciones previas.
3. El caso de uso solicita la operación a través de la interfaz de comunicación.
4. El adaptador envía el comando al entorno externo.
5. El entorno devuelve una respuesta o produce un evento.
6. La aplicación procesa y valida la información recibida.
7. Se actualiza la sesión de ejecución.
8. La interfaz refleja el nuevo estado.
9. Se registra el evento correspondiente.

Este diagrama será una de las mejores herramientas para defender la separación entre la capa gráfica y el runtime.

---

# 6. Cómo documentar el código sin convertir la tesis en un inventario de clases

No es necesario explicar todas las clases de `malbec-s88` con la misma profundidad.

Selecciona aquellas que permitan comprender las decisiones fundamentales y el flujo de datos.

Para cada clase importante, utiliza una ficha breve con los siguientes puntos:

**Responsabilidad:** qué problema resuelve.

**Datos principales:** qué información mantiene o procesa.

**Dependencias:** con qué componentes se relaciona.

**Operaciones relevantes:** qué métodos expresan sus responsabilidades.

**Decisiones de diseño:** por qué está separada de otras clases.

**Pruebas:** cómo se comprueba su comportamiento.

**Relación con los requisitos:** qué funcionalidad permite satisfacer.

Por ejemplo, en la sección del editor SFC, puedes explicar cómo la estructura procedimental se transforma en una representación gráfica.

En la sección del modelo de dominio, puedes explicar cómo se representan los pasos y las transiciones.

En la sección de casos de uso, puedes mostrar cómo se modifica esa estructura.

Así, el lector comprende la colaboración entre los componentes sin necesitar una descripción individual de cada método.

Incluye fragmentos de código solamente cuando demuestren un punto técnico concreto: una regla de validación, una interfaz de aplicación, la forma de gestionar los cambios del modelo, una operación de persistencia o el cálculo de una disposición gráfica.

Cada fragmento debe ir acompañado de una explicación y de su relación con una decisión de diseño.

---

# 7. Manual técnico y manual de usuario

Estos documentos son entregables independientes de la memoria académica, aunque pueden incorporarse como anexos.

No deberían compartir exactamente la misma estructura, porque están dirigidos a lectores diferentes.

## 7.1. Manual técnico

Estará dirigido a desarrolladores y personas responsables de mantener o ampliar el sistema.

Te recomiendo la siguiente estructura:

1. Introducción y alcance.
2. Arquitectura general.
3. Estructura del repositorio.
4. Dependencias entre módulos.
5. Responsabilidades de los componentes principales.
6. Modelo de dominio ISA-88.
7. Casos de uso y lógica de aplicación.
8. Persistencia y formatos de datos.
9. Editor de modelo físico.
10. Editor de recetas y representación SFC.
11. Arquitectura de comunicación y runtime.
12. Integración con EPICS.
13. Registro de eventos y reportes.
14. Configuración del entorno de desarrollo.
15. Compilación y ejecución.
16. Ejecución de las pruebas.
17. Extensión del sistema.
18. Limitaciones conocidas y diagnóstico de errores.

Este manual debe permitir que otra persona pueda orientarse en el código, identificar dónde debe introducir un cambio y comprender qué pruebas debe ejecutar después.

## 7.2. Manual de usuario

Estará dirigido principalmente a los ingenieros de procesos y a los operadores, de acuerdo con las funciones que tenga realmente la aplicación.

La estructura recomendada es:

1. Introducción.
2. Requisitos previos.
3. Instalación y apertura de Malbec.
4. Creación de un proyecto.
5. Creación y configuración de la planta.
6. Gestión de elementos físicos.
7. Creación de un conjunto de recetas.
8. Creación y edición de una receta.
9. Modificación del procedimiento SFC.
10. Asociación de pasos con equipos.
11. Configuración de parámetros.
12. Validación, guardado y carga de recetas.
13. Preparación de una ejecución.
14. Uso del simulador, si existe.
15. Supervisión de la ejecución.
16. Consulta de logs y reportes.
17. Tratamiento de errores habituales.
18. Recomendaciones de uso y precauciones operacionales.

Acompaña los procedimientos con capturas de pantalla reales. Cada figura debe identificar la pantalla y explicar la acción realizada y el resultado esperado.

---

# 8. Plan de pruebas que deberías cerrar antes de entregar

El anteproyecto contempla tanto pruebas unitarias como validación en un entorno controlado, además de manuales técnicos y de usuario. Por ello, las pruebas deben tener una sección propia en los resultados y no limitarse a una lista al final. Anteproyecto Daniel Campos

Te recomiendo utilizar un plan con cuatro niveles.

| Nivel | Qué demuestra | Evidencia |
|---|---|---|
| Unitario | Que las clases del modelo y los casos de uso cumplen sus contratos. | Informes de pruebas automatizadas. |
| Integración | Que los módulos intercambian información y conservan la consistencia. | Pruebas entre módulos y resultados de ejecución. |
| Caja negra | Que las funcionalidades se comportan según los requisitos desde la perspectiva del usuario. | Casos de prueba funcionales y capturas. |
| Extremo a extremo | Que el flujo completo de creación, preparación, ejecución y registro funciona dentro del entorno validado. | Receta de prueba, eventos y reporte final. |

Para el runtime, añade pruebas de errores de comunicación y de consistencia del estado.

En especial, verifica la diferencia entre una orden enviada, una orden aceptada y un cambio de estado confirmado. No deben tratarse como si fueran el mismo evento.

Si existe simulación y comunicación real, documenta ambas por separado. Una prueba satisfactoria en el simulador no demuestra por sí misma que el adaptador real, el servidor EPICS y el PLC funcionen correctamente.

---

# 9. Matriz final para controlar el cumplimiento de los objetivos

Te recomiendo utilizar la siguiente matriz durante todo el desarrollo y actualizarla antes de entregar la tesis.

| Objetivo específico del anteproyecto | Qué debes producir | Qué debe aparecer en la tesis | Qué debes comprobar |
|---|---|---|---|
| Identificar los requisitos técnicos y jerarquías ISA-88 | Modelo de dominio y reglas identificadas. | Marco teórico, diagnóstico, modelo de dominio y requisitos. | Que el modelo represente los conceptos utilizados y que las restricciones relevantes estén documentadas. |
| Identificar los requisitos funcionales y no funcionales | Especificación priorizada y verificable. | Requisitos, actores, casos de uso y riesgos. | Que cada requisito tenga un criterio de aceptación o una forma de verificación. |
| Diseñar los módulos necesarios | Arquitectura y diseño de componentes. | Diagramas, clases de análisis, interacciones, decisiones arquitectónicas y modelo de persistencia. | Que los diagramas coincidan con la implementación o identifiquen claramente qué está propuesto. |
| Codificar e integrar los módulos | Funcionalidades implementadas e integradas. | Modelo de dominio, casos de uso, editores, comunicación, runtime y reportes. | Que las funcionalidades se ejecuten y que los cambios no contradigan la arquitectura documentada. |
| Validar el sistema en un entorno controlado | Plan y resultados de pruebas. | Pruebas unitarias, integración, caja negra y escenarios de extremo a extremo. | Que los resultados estén respaldados por evidencia y se distingan las funcionalidades verificadas de las pendientes. |

Esta tabla es la columna vertebral de la memoria: si un resultado no contribuye a un objetivo, no tiene por qué ocupar una sección extensa; si un objetivo no tiene evidencia asociada, todavía no está suficientemente defendido.

---

# 10. Roadmap inmediato: qué haría ahora, en orden

Para pasar del plan a la ejecución, seguiría este orden:

**Primero**, cerraría el inventario del estado inicial y de la implementación actual. Identificaría qué clases existían, qué aportaste tú, cuáles son las funcionalidades terminadas y qué queda pendiente. También ejecutaría la batería de pruebas disponible y registraría su resultado.

**Segundo**, consolidaría los requisitos y completaría el editor de recetas. El objetivo sería tener un modelo de receta válido, con edición y persistencia verificables, antes de desarrollar el runtime.

**Tercero**, definiría el contrato de ejecución y la arquitectura de comunicación. Aquí resolvería la relación real entre Malbec, EPICS, PLC4X, Merlot y el PLC. No implementaría comandos hasta haber documentado las señales, las operaciones y el comportamiento esperado del sistema externo.

**Cuarto**, desarrollaría el simulador y el modelo de ejecución. De esta forma, las reglas de supervisión, el registro de eventos y la representación del estado podrían probarse sin depender inicialmente de la infraestructura industrial.

**Quinto**, implementaría el adaptador real de comunicación, integrándolo con los componentes de ejecución sin trasladar detalles de transporte a los modelos y las interfaces gráficas.

**Sexto**, completaría la visualización del runtime, los logs y los reportes. Después ejecutaría los escenarios de validación funcional y de integración.

**Séptimo**, cerraría la documentación técnica y el manual de usuario en paralelo con las pruebas finales. Así, la documentación estará respaldada por funcionalidades que realmente existen.

**Octavo**, redactaría las conclusiones a partir de la matriz de trazabilidad, indicando de forma explícita qué objetivos fueron alcanzados y qué mejoras quedan fuera del alcance.

## La recomendación arquitectónica más importante

Dado el estado que describes, no intentaría introducir toda la lógica de ejecución dentro del editor de recetas. Mantendría separadas la definición procedimental, la sesión de ejecución, la comunicación externa y la presentación gráfica.

Puedes aprovechar el módulo `batch` como punto de partida para estudiar dónde ubicar las responsabilidades de runtime. Si el diseño lo justifica, también puedes añadir un adaptador de comunicación independiente. La decisión debe basarse en las dependencias reales y en la necesidad de mantener testeable el núcleo, no en conseguir que el repositorio tenga más módulos.

## La recomendación académica más importante

No conviertas la tesis en una narración de todo lo que programaste en orden cronológico. Estructúrala alrededor de **problemas, requisitos, decisiones, implementación y evidencia**.

En cada sección importante deberías poder responder cuatro preguntas:

1. ¿Qué problema o requisito debía resolverse?
2. ¿Qué solución se diseñó y por qué?
3. ¿Cómo se implementó en `malbec-s88`?
4. ¿Cómo se verificó que satisface el requisito?

Si mantienes ese hilo conductor desde el planteamiento del problema hasta las conclusiones, la tesis podrá ser extensa sin convertirse en una recopilación desordenada de código.

En definitiva, **tu mejor estructura es la del ejemplo de tesis, pero adaptada al desarrollo real de `malbec-s88`, con una fase de requisitos rigurosa, una explicación explícita de la arquitectura híbrida, un capítulo de resultados organizado por las fases de desarrollo y una validación basada en trazabilidad**. Esa estructura permite documentar tanto el trabajo ya realizado en los editores como el trabajo pendiente de comunicación, runtime y supervisión, sin confundir las funcionalidades implementadas con las que todavía deben construirse.
