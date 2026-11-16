# Práctica 9: Diagrama de clases (UML) del módulo del Hub
**Sistema de Gestión de Información de Proyectos Agroecológicos de CEDICAM (SIGIPA-CEDICAM)**

| Concepto | Datos de la entrega |
| :--- | :--- |
| **Institución** | Tecnológico Nacional de México · Instituto Tecnológico de Tlaxiaco |
| **Programa Educativo** | Ingeniería en Sistemas Computacionales |
| **Asignatura** | Fundamentos de Ingeniería de Software (SCC-1007) · Unidad 4: Modelo de análisis (Sprint 4) |
| **Organización / Cliente** | CEDICAM - Centro de Desarrollo Integral Campesino de la Mixteca (Coordinador Técnico) |
| **Integrantes del equipo** | Evelyn Sosa José, Irwing Sael Feria Caballero, Ebenezer Acevedo Nicolas, Jazmin Aguilar Rojas |
| **Grupo y Docente** | Grupo 5BS · Docente: Ing. José Alfredo Román Cruz |
| **Entregables del repositorio** | `modelo/OpenHub-Equipo.qea`, `docs/analisis/diagrama-clases.png`, `docs/analisis/trazabilidad.csv` |
| **Fecha de elaboración** | 12 de noviembre de 2026 · Tlaxiaco, Oaxaca |

---

## Introducción

> *"Educación, ciencia y tecnología, progreso día con día"*

Este documento presenta el modelo de análisis del sistema **SIGIPA-CEDICAM** (Sistema de Gestión de Información de Proyectos Agroecológicos del Centro de Desarrollo Integral Campesino de la Mixteca), expresado mediante un diagrama de clases en lenguaje UML. El propósito del sistema es dotar a los promotores comunitarios y coordinadores de CEDICAM de una herramienta móvil operable en dispositivos Android de gama baja y en modo *offline* para centralizar registros de proyectos agroecológicos, participantes, obras de conservación de suelo y agua, y capacitaciones en la región Mixteca Alta de Oaxaca.

Durante la elicitación realizada en la Práctica 7 con el Coordinador Técnico de CEDICAM, se identificó que la información operativa se encontraba dispersa en libretas físicas y archivos personales, generando riesgo de pérdida de memoria institucional ante la rotación de personal técnico y dificultades para consolidar reportes dirigidos a donantes. Estas necesidades se formalizaron en el Documento de Especificación de Requisitos de Software (DRS) versión 1.0, validado el 10 de octubre de 2026 con diez requisitos funcionales (`RF-01` a `RF-10`) y ocho requisitos no funcionales (`RNF-01` a `RNF-08`).

Para traducir dichos requisitos a una representación técnica orientada a objetos, se aplicó la técnica de **carrera de sustantivos depurada** sobre los enunciados y criterios de aceptación del DRS. El modelo resultante consta de cuatro clases núcleo de dominio en el diagrama principal (`Proyecto`, `Comunidad`, `Participante` y `ObraConservacion`), complementadas por las entidades de apoyo funcional del módulo (`Capacitacion`, `TecnicaAgroecologica`, `ZonaRecargaHidrica` y `ReporteDonante`) y sus respectivas enumeraciones, alojadas en el paquete `Análisis` dentro de Enterprise Architect.

---

## 1. Clases identificadas desde el DRS

Se aplicó la carrera de sustantivos sobre los requisitos funcionales (`RF-01` a `RF-10`) y el glosario de dominio del DRS v1.0. Cada candidato fue evaluado para conservarse como clase de análisis usando el vocabulario exacto de CEDICAM, convertirse en atributo tipado, expresarse como operación o descartarse por corresponder a infraestructura externa o interfaz de usuario:

| Sustantivo del DRS | Decisión de modelado | Justificación técnica y cobertura de requisitos (DRS v1.0) |
| :--- | :--- | :--- |
| **Proyecto agroecológico** | **Clase `Proyecto`** | Entidad central que articula participantes, obras y reportes para donantes (`RF-01`, `RF-03`, `RF-04`, `RF-06`). |
| **Comunidad, localidad** | **Clase `Comunidad`** | Unidad territorial base donde residen los beneficiarios y se ejecutan obras, capacitaciones y técnicas (`RF-02`, `RF-05`, `RF-07`, `RF-08`, `RF-09`). |
| **Participante, beneficiario, líder comunitario** | **Clase `Participante`** (con atributo `rol: RolParticipante`) | Actores vinculados a comunidades y proyectos; se distinguen mediante su rol y datos de contacto sin duplicar registros (`RF-01`, `RF-04`, `RF-08`). |
| **Obra de conservación de suelo y agua** | **Clase `ObraConservacion`** | Intervención física (terrazas, zanjas de infiltración, bordos, presas de gaviones) con tipo, ubicación de referencia, estado y responsable técnico (`RF-02`, `RF-06`). |
| **Taller, capacitación** | **Clase `Capacitacion`** (apoyo funcional) | Evento formativo impartido en una localidad con tema, fecha, número de asistentes y facilitador (`RF-03`, `RF-05`). |
| **Técnica agroecológica, resultado** | **Clase `TecnicaAgroecologica`** (apoyo funcional) | Práctica agroecológica aplicada en una comunidad con fecha y resultado cualitativo o cuantitativo (`RF-07`). |
| **Zona de recarga hídrica** | **Atributos en `Comunidad` / Clase `ZonaRecargaHidrica`** | En el diagrama núcleo se sintetiza mediante `zonaRecargaHidrica` y `referenciaMapa` dentro de `Comunidad` para consulta rápida en modo *offline* (`RF-09`). |
| **Reporte de avance, indicadores** | **Operación `exportarReporte(): Reporte`** | Consolidación calculada de avances y beneficiarios por proyecto o comunidad en formatos PDF/CSV (`RF-06`). |
| **Fecha, tipo, estado, ubicación, contacto** | **Atributos tipados** | Propiedades intrínsecas de sus respectivas clases; se evita crear clases artificiales como `«Fecha»` o `«Estado»`. |
| **Sistema, pantalla, SQLite, sincronización** | **Descartados del dominio** | Corresponden a restricciones técnicas de arquitectura, persistencia local o interfaz (`RF-10`, `RNF-01` a `RNF-08`), no a objetos del negocio. |

---

## 2. Atributos y tipos de datos

Para garantizar consistencia semántica y evitar cadenas de texto genéricas (`String`) en atributos con dominios acotados definidos por el DRS, se establecen los siguientes tipos enumerados y estructurados de apoyo:

### 2.1 Enumeraciones y tipos estructurados del dominio

| Tipo / Enumeración | Valores permitidos / Estructura | Requisito asociado |
| :--- | :--- | :--- |
| **`EstadoProyecto`** | `planificado`, `activo`, `concluido` | `RF-01`, `RF-06` |
| **`EstadoObra`** | `planificada`, `en_proceso`, `concluida` | `RF-02` (Criterio 1) |
| **`RolParticipante`** | `beneficiario`, `lider_comunitario`, `promotor`, `facilitador` | `RF-01`, `RF-03`, `RF-04`, `RF-08` |
| **`TipoObra`** | `terraza`, `zanja_infiltracion`, `bordo`, `presa_gaviones` | `RF-02`, Definiciones DRS |
| **`Reporte`** | Estructura exportable en formato `PDF` o `CSV` con métricas consolidadas de avance, obras y beneficiarios | `RF-06` (Criterios 1 y 2) |

### 2.2 Especificación de atributos por clase (sin redundancia de claves foráneas)

Cada clase contiene exclusivamente los atributos propios requeridos por el DRS con tipos de datos coherentes. **Ningún atributo repite una relación estructural** (por ejemplo, no existe `comunidadId` en `Participante` ni `proyectoId` en `ObraConservacion`, ya que dichos vínculos se representan mediante asociaciones explícitas).

#### A. Clase `Proyecto` (`«Project»`)
* **Requisitos asociados:** `RF-01`, `RF-03`, `RF-04`, `RF-06`.

| Visibilidad y Atributo | Tipo de dato | Descripción y trazabilidad |
| :--- | :--- | :--- |
| `- id` | `Integer` | Identificador único interno del proyecto. |
| `- nombre` | `String` | Nombre oficial del proyecto agroecológico (`RF-01`). |
| `- descripcion` | `String` | Objetivo general y alcance operativo del proyecto. |
| `- fechaInicio` | `Date` | Fecha formal de arranque en campo. |
| `- fechaFin` | `Date` | Fecha programada o real de cierre del proyecto. |
| `- estado` | `EstadoProyecto` | Estado operativo vigente del proyecto (`planificado`, `activo`, `concluido`). |

#### B. Clase `Comunidad` (`«Localidad»`)
* **Requisitos asociados:** `RF-02`, `RF-05`, `RF-07`, `RF-08`, `RF-09`.

| Visibilidad y Atributo | Tipo de dato | Descripción y trazabilidad |
| :--- | :--- | :--- |
| `- id` | `Integer` | Identificador único de la comunidad o localidad. |
| `- nombre` | `String` | Nombre oficial de la comunidad (`RF-01`, `RF-05`). |
| `- municipio` | `String` | Municipio de la región Mixteca al que pertenece. |
| `- zonaRecargaHidrica` | `String` | Descripción geográfica o paraje de recarga hídrica identificada (`RF-09`). |
| `- referenciaMapa` | `String` | Coordenadas o ruta local de imagen ligera de referencia hídrica (`RF-09`). |

#### C. Clase `Participante` (`«Beneficiario»`)
* **Requisitos asociados:** `RF-01`, `RF-03`, `RF-04`, `RF-08`.

| Visibilidad y Atributo | Tipo de dato | Descripción y trazabilidad |
| :--- | :--- | :--- |
| `- id` | `Integer` | Identificador único del participante en el padrón centralizado. |
| `- nombre` | `String` | Nombre del participante o líder comunitario (`RF-01`, `RF-08`). |
| `- rol` | `RolParticipante` | Rol o cargo desempeñado en el proyecto o localidad (`RF-01`, `RF-08`). |
| `- contacto` | `String` | Teléfono o medio de contacto comunitario (`RF-01`, `RF-08`). |

#### D. Clase `ObraConservacion` (`«CivilWork»`)
* **Requisitos asociados:** `RF-02`, `RF-06`.

| Visibilidad y Atributo | Tipo de dato | Descripción y trazabilidad |
| :--- | :--- | :--- |
| `- id` | `Integer` | Identificador único de la obra de conservación. |
| `- tipo` | `TipoObra` | Tipo de obra física (`terraza`, `zanja_infiltracion`, `bordo`, `presa_gaviones`) (`RF-02`). |
| `- ubicacionReferencia` | `String` | Paraje o referencia geográfica dentro de la comunidad (`RF-02`). |
| `- fechaRegistro` | `Date` | Fecha de captura o actualización del avance de la obra (`RF-02`). |
| `- estadoActual` | `EstadoObra` | Estado de ejecución (`planificada`, `en_proceso`, `concluida`) (`RF-02`). |
| `- responsableTecnico` | `String` | Cargo o identificador del promotor técnico responsable del seguimiento en campo (`RF-02`). |

---

## 3. Operaciones ligadas a los requisitos

Cada operación deriva directamente de los verbos identificados en los requisitos funcionales (`RF-01` a `RF-09`), se aloja en la clase poseedora de los datos que manipula y especifica explícitamente sus **parámetros y tipo de retorno**:

| Clase | Visibilidad y Firma de la Operación | Retorno | Requisito (RF) | Descripción funcional |
| :--- | :--- | :--- | :--- | :--- |
| **`Proyecto`** | `+ registrar(nombre: String, fInicio: Date)` | `Boolean` | `RF-01`, `RNF-01` | Persiste un nuevo proyecto agroecológico en almacenamiento local SQLite tras validar campos. |
| **`Proyecto`** | `+ getProgresos()` | `List` | `RF-02`, `RF-06` | Recupera y consolida el porcentaje de avance de todas las obras vinculadas al proyecto. |
| **`Proyecto`** | `+ finalizar(fechaCierre: Date)` | `Boolean` | `RF-01`, `RF-06` | Cambia el estado del proyecto a `concluido` tras validar entregables. |
| **`Proyecto`** | `+ exportarReporte(formato: String)` | `Reporte` | `RF-06` | Genera el reporte exportable (`PDF` o `CSV`) con indicadores de avance para donantes. |
| **`Comunidad`** | `+ actualizar(zona: String, refMapa: String)` | `Boolean` | `RF-09` | Actualiza datos territoriales y referencias de recarga hídrica en modo *offline*. |
| **`Comunidad`** | `+ obtenerHistorial()` | `List` | `RF-05`, `RNF-07` | Consulta consolidada de capacitaciones, obras y técnicas aplicadas en la localidad en $\le 2$ clics. |
| **`Participante`** | `- validar()` | `Boolean` | `RF-01` | Operación auxiliar privada que verifica reglas de integridad y campos obligatorios antes de guardar. |
| **`Participante`** | `+ registrar(nombre: String, rol: RolParticipante)` | `Boolean` | `RF-01`, `RF-04`, `RF-08` | Da de alta al participante o líder comunitario en el padrón local. |
| **`Participante`** | `+ asociarAProyecto(p: Proyecto)` | `Boolean` | `RF-01`, `RF-04` | Vincula al participante con uno o múltiples proyectos agroecológicos. |
| **`Participante`** | `+ participarEnCapacitacion(t: Capacitacion)` | `Boolean` | `RF-03` | Registra la asistencia y participación del actor en talleres formativos impartidos. |
| **`ObraConservacion`** | `- procesoReporte()` | `binary` | `RF-06` | Método interno privado que serializa métricas binarias de avance para ser consumidas por `exportarReporte()`. |
| **`ObraConservacion`** | `+ crear(tipo: TipoObra, ubicacion: String)` | `Boolean` | `RF-02` | Registra una nueva obra de conservación de suelo o agua con persistencia *offline*. |
| **`ObraConservacion`** | `+ actualizarEstado(e: EstadoObra)` | `Boolean` | `RF-02` | Actualiza la etapa de avance de la obra (`planificada` $\rightarrow$ `en_proceso` $\rightarrow$ `concluida`). |
| **`ObraConservacion`** | `+ obtenerUbicacion()` | `String` | `RF-02` | Devuelve la referencia espacial de la obra vinculada a su comunidad. |

---

## 4. Encapsulamiento y visibilidad

El modelo aplica estrictamente el principio de ocultamiento de información de la orientación a objetos:

* **Atributos privados (`-`):** El **100 % de los atributos** en todas las clases (`-id`, `-nombre`, `-estado`, `-zonaRecargaHidrica`, `-estadoActual`, etc.) están declarados como privados (`-`) para proteger la integridad de los registros locales en SQLite (`RNF-05`) y evitar modificaciones directas sin validación.
* **Operaciones públicas (`+`):** Todas las operaciones de negocio invocadas desde los casos de uso y flujos de usuario (`+registrar()`, `+exportarReporte()`, `+obtenerHistorial()`, `+actualizarEstado()`) tienen visibilidad pública (`+`).
* **Justificación de excepciones internas — Operaciones privadas (`-`):** Se emplean exactamente dos operaciones con visibilidad privada (`-`) por razones de diseño:
  1. `-validar(): Boolean` en `Participante`: encapsula las reglas internas de validación de datos antes de confirmar el alta en el padrón, evitando que clases externas invoquen validaciones parciales.
  2. `-procesoReporte(): binary` en `ObraConservacion`: rutina interna de empaquetado binario de métricas de avance que solo debe ser invocada internamente durante la consolidación de reportes (`RF-06`).
* **Visibilidad protegida (`#`):** No se utiliza en ninguna clase debido a que el modelo prescinde de jerarquías de herencia.

---

## 5. Asociaciones y multiplicidad

Todas las relaciones entre clases cuentan con nombre descriptivo de acción y multiplicidad explícita en ambos extremos, reflejando las reglas de negocio validadas con CEDICAM:

| Clase Origen (A) | Asociación | Clase Destino (B) | Mult. A | Mult. B | Regla de negocio validada (DRS) |
| :--- | :--- | :--- | :---: | :---: | :--- |
| **`Comunidad`** | `ubica_en` | **`Proyecto`** | `1` | `0..*` | Una comunidad puede albergar cero o muchos proyectos agroecológicos (`RF-01`, `RF-05`). |
| **`Comunidad`** | `pertenece_a` | **`Participante`** | `1` | `0..*` | Cada participante reside en una comunidad base; una comunidad tiene cero o múltiples participantes registrados (`RF-01`, `RF-04`). |
| **`Proyecto`** | `integra` | **`Participante`** | `1..*` | `1..*` | Un participante puede asociarse a uno o más proyectos y cada proyecto integra al menos un participante (`RF-01`, `RF-04`). |
| **`Proyecto`** | `pertenece_a_proyecto` | **`ObraConservacion`** | `1` | `1..*` | Cada obra de conservación se enmarca en exactamente un proyecto y todo proyecto contempla una o más obras (`RF-02`). |
| **`ObraConservacion`** | `se_construye_en` | **`Comunidad`** | `0..*` | `1` | Toda obra se construye en una comunidad específica; una comunidad acumula cero o varias obras físicas (`RF-02`). |

---

## 6. Agregación, composición y herencia

**Justificación técnica de ausencia de relaciones especiales:** Se optó por asociaciones simples con multiplicidad explícita y estereotipos de rol (`«Project»`, `«Localidad»`, `«Beneficiario»`, `«CivilWork»`), prescindiendo de agregación, composición y herencia en el núcleo del dominio por las siguientes razones verificadas con el DRS:

* **Por qué no se usa composición ni agregación en el núcleo:** Tanto `Participante` como `Comunidad` y `ObraConservacion` conservan **existencia histórica independiente** dentro de la memoria institucional de CEDICAM. Si un proyecto concluye o se cancela, las comunidades, los beneficiarios y las obras físicas construidas en campo (terrazas, zanjas, presas de gaviones) siguen existiendo en el padrón histórico (`RF-04`, `RF-07`). Utilizar composición implicaría una dependencia de ciclo de vida que eliminaría registros históricos al cerrar un proyecto.
* **Por qué no se usa herencia:** Los actores comunitarios (beneficiarios, líderes comunitarios aliados, promotores y facilitadores) comparten exactamente la misma estructura de atributos de identificación, comunidad de origen y contacto (`RF-01`, `RF-08`). Modelarlos mediante herencia crearía tablas y registros duplicados cuando un mismo beneficiario asume el rol de líder comunitario o facilitador en otro taller; por ello se modela una única clase `Participante` diferenciada por su atributo `rol: RolParticipante`.

---

## 7. Trazabilidad con el DRS y el BPMN

Cada clase del diagrama en Enterprise Architect incluye una nota vinculada con sus requisitos (`RF` / `RNF`), y la matriz completa se encuentra respaldada tanto en la siguiente tabla como en el archivo `docs/analisis/trazabilidad.csv`, manteniendo consistencia nominal con el DRS v1.0 y los procesos BPMN de la Unidad 2:

| Elemento UML | Tipo | Requisitos DRS | Origen Entrevista (P7) | Proceso BPMN (Unidad 2) | Responsabilidad funcional |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`Proyecto`** | Clase núcleo | `RF-01`, `RF-03`, `RF-04`, `RF-06` | Preguntas 5, 6 | Gestión de proyectos agroecológicos | Articulación de proyectos y exportación de reportes a donantes. |
| **`Comunidad`** | Clase núcleo | `RF-02`, `RF-05`, `RF-07`, `RF-08`, `RF-09`, `RNF-07` | Preguntas 5, 6, 7 | Consulta histórica y territorial | Consolidación por localidad, zonas de recarga hídrica e historial en $\le 2$ clics. |
| **`Participante`** | Clase núcleo | `RF-01`, `RF-03`, `RF-04`, `RF-08` | Preguntas 5, 6, 7 | Registro de padrón y líderes | Gestión centralizada de beneficiarios, contactos y asignación a proyectos. |
| **`ObraConservacion`** | Clase núcleo | `RF-02`, `RF-06` | Preguntas 5, 6 | Conservación de suelo y agua | Seguimiento de tipo, ubicación y estado de avance de obras físicas. |
| **`Capacitacion`** | Clase apoyo | `RF-03`, `RF-05` | Preguntas 5, 6, 7 | Capacitaciones comunitarias | Registro de talleres impartidos e historial cronológico por localidad. |
| **`TecnicaAgroecologica`** | Clase apoyo | `RF-07` | Pregunta 7 | Preservación técnica | Registro de prácticas agroecológicas aplicadas y resultados obtenidos. |

---

## 8. Notación UML y uso de Enterprise Architect

![Diagrama de clases UML - Módulo núcleo SIGIPA-CEDICAM](../docs/analisis/diagrama-clases.png)
*Figura 1. Diagrama de clases UML — Módulo núcleo del sistema SIGIPA-CEDICAM (Paquete de Análisis).*

* **Ubicación en el modelo:** El diagrama se construyó dentro del archivo **`modelo/OpenHub-Equipo.qea`** en Enterprise Architect, ubicado en el paquete **`Análisis`** bajo un diagrama formal de tipo **`Class`**.
* **Convenciones de nomenclatura:** Todas las clases y enumeraciones siguen estrictamente **`UpperCamelCase`** (`Proyecto`, `Comunidad`, `Participante`, `ObraConservacion`, `EstadoProyecto`, `TipoObra`), mientras que los atributos, operaciones y nombres de asociación siguen **`lowerCamelCase`** (`fechaInicio`, `zonaRecargaHidrica`, `ubicacionReferencia`, `exportarReporte()`).
* **Legibilidad y exportación:** Los conectores se trazaron de forma ortogonal evitando cruces de líneas, acompañados de leyenda de visibilidad (`-` privado, `+` público) y notas de trazabilidad hacia los RF del DRS, exportando la imagen a **`docs/analisis/diagrama-clases.png`** con resolución superior a `800 px` de ancho.

---

## 9. Trabajo por pull request

### 9.1 Flujo de trabajo colaborativo (eduScrum)
Para evitar conflictos binarios en el archivo `.qea` y asegurar la participación verificable en *Insights > Contributors*, el equipo trabajó por turnos mediante ramas temáticas y *pull requests* revisados con comentarios concretos por un compañero distinto al autor antes de su fusión:

| Integrante del equipo | Pull request como autor | Pull request como revisor |
| :--- | :--- | :--- |
| **Evelyn Sosa José** | **PR #1** — Clases base `Proyecto` y `Comunidad` en `.qea` y documento de análisis | **PR #2** — Revisión de `Participante`, multiplicidades y notas de trazabilidad |
| **Irwing Sael Feria Caballero** | **PR #2** — Clase `Participante`, enumeraciones y asociaciones | **PR #3** — Revisión de `ObraConservacion`, tipos de retorno y encapsulamiento |
| **Ebenezer Acevedo Nicolas** | **PR #3** — Clase `ObraConservacion` y justificación de encapsulamiento/herencia | **PR #4** — Revisión de `trazabilidad.csv`, imagen PNG (`>800px`) y entrega final |
| **Jazmin Aguilar Rojas** | **PR #4** — Matriz `trazabilidad.csv`, exportación PNG y consolidación de entrega | **PR #1** — Revisión de clases base, nomenclatura `CamelCase` y notas RF |

---

## 10. Revisión cruzada y mejora

El modelo fue evaluado por un equipo revisor par utilizando la rúbrica oficial de la Práctica 9 antes de la integración final a la rama `main`:

| Observación del equipo revisor | Estado | Qué cambió y por qué |
| :--- | :--- | :--- |
| **1. Multiplicidad entre `Proyecto` y `Participante`:** Verificar que permita participación en múltiples proyectos según `RF-01` y `RF-04`. | Corregido en PR | Se estableció multiplicidad **`1..*` a `1..*`** en la asociación `integra` para reflejar que un beneficiario participa en uno o varios proyectos agroecológicos simultáneos. |
| **2. Ausencia de herencia y composición (Criterio 6):** Documentar por escrito por qué no se empleó herencia ni composición en el núcleo del modelo. | Justificado y documentado | Se agregó la justificación explícita en la **Sección 6** explicando la independencia histórica de comunidades, obras y beneficiarios ante el cierre de proyectos. |
| **3. Visibilidad de métodos internos (Criterio 4):** Confirmar la visibilidad privada de los métodos auxiliares `-validar()` y `-procesoReporte()`. | Justificado en PR | Se documentó en la **Sección 4** que son rutinas internas de validación y serialización invocadas únicamente dentro de su propia clase. |
| **4. Precisión de tipos enumerados y firmas (Criterios 2 y 3):** Sustituir `String` por enumeraciones del dominio (`EstadoProyecto`, `TipoObra`, `EstadoObra`, `RolParticipante`) y especificar parámetros y retornos en las operaciones. | Corregido en PR | Se reemplazaron los tipos genéricos por las enumeraciones definidas en la **Sección 2.1** y se completaron las firmas (`parámetros: Tipo` y `: Retorno`) en la **Sección 3** para eliminar ambigüedades de diseño. |
| **5. Protección de datos personales (Requisitos mínimos):** Evitar nombres completos de personas de la organización en el documento y repositorio. | Corregido en PR | Se sustituyó el nombre completo del contacto de CEDICAM por su cargo institucional (*Coordinador Técnico de CEDICAM*) en todo el documento para cumplir con la política de repositorio público. |