# Práctica 9: Diagrama de clases (UML) del módulo del Hub
## Sistema de Gestión de Información de Proyectos Agroecológicos de CEDICAM (SIGIPA-CEDICAM)

**SECRETARÍA DE EDUCACIÓN PÚBLICA**  
**TECNOLÓGICO NACIONAL DE MÉXICO**  
**Instituto Tecnológico de Tlaxiaco**  
**Ingeniería en Sistemas Computacionales**

| Concepto | Datos de la entrega |
| :--- | :--- |
| **Asignatura** | Fundamentos de Ingeniería de Software (SCC-1007)<br>Unidad 4: Modelo de análisis (Sprint 4) |
| **Organización / Cliente** | CEDICAM - Centro de Desarrollo Integral Campesino de la Mixteca (Ing. Maximiliano Hernández) |
| **Integrantes del equipo** | Evelyn Sosa José<br>Irwing Sael Feria Caballero<br>Ebenezer Acevedo Nicolas<br>Jazmin Aguilar Rojas |
| **Grupo y Docente** | Grupo 5BS<br>Docente: Ing. José Alfredo Román Cruz |
| **Entregables del repositorio** | `modelo/OpenHub-Equipo.qea`<br>`docs/analisis/diagrama-clases.png`<br>`docs/analisis/trazabilidad.csv` |
| **Fecha de elaboración** | 12 de noviembre de 2026<br>Tlaxiaco, Oaxaca |

---

## 1. Introducción

> *"Educación, ciencia y tecnología, progreso día con día"*

Este documento presenta el modelo de análisis del sistema **SIGIPA-CEDICAM** (Sistema de Gestión de Información de Proyectos Agroecológicos del Centro de Desarrollo Integral Campesino de la Mixteca), expresado mediante un diagrama de clases en lenguaje UML. El propósito del sistema es dotar a los promotores comunitarios y coordinadores de CEDICAM de una herramienta móvil operable en dispositivos Android de gama baja y en modo *offline* para centralizar registros de proyectos agroecológicos, participantes, obras de conservación de suelo y agua, y capacitaciones en la región Mixteca Alta de Oaxaca.

Durante la elicitación realizada en la Práctica 7 con el Ing. Maximiliano Hernández, se identificó que la información operativa se encontraba dispersa en libretas físicas y archivos personales, generando riesgo de pérdida de memoria institucional ante la rotación de personal técnico y dificultades para consolidar reportes dirigidos a donantes. Estas necesidades se formalizaron en el Documento de Especificación de Requisitos de Software (DRS) versión 1.0, validado el 10 de octubre de 2026 con diez requisitos funcionales (RF-01 a RF-10) y ocho requisitos no funcionales (RNF-01 a RNF-08).

Para traducir dichos requisitos a una representación técnica orientada a objetos, se aplicó la técnica de carrera de sustantivos depurada sobre los enunciados y criterios de aceptación del DRS. El modelo resultante consta de cuatro clases núcleo de dominio en el diagrama principal (`Proyecto`, `Comunidad`, `Participante` y `ObraConservacion`), complementadas por las entidades de apoyo funcional del módulo (`Capacitacion`, `TecnicaAgroecologica`, `ZonaRecargaHidrica` y `ReporteDonante`) y las enumeraciones correspondientes, alojadas en el paquete *Análisis* de Enterprise Architect.

---

## 2. Identificación de clases desde el DRS

Se aplicó la carrera de sustantivos sobre los requisitos funcionales (RF-01 a RF-10) y el glosario de dominio del DRS v1.0. Cada candidato fue evaluado para conservarse como clase de análisis, convertirse en atributo tipado, expresarse como operación o descartarse por corresponder a infraestructura externa o interfaz.

| Sustantivo del DRS | Decisión de modelado | Justificación técnica (DRS v1.0) |
| :--- | :--- | :--- |
| Proyecto agroecológico | Clase `Proyecto` | Entidad central que articula participantes, obras y reportes para donantes (RF-01, RF-03, RF-04, RF-06). |
| Comunidad, localidad | Clase `Comunidad` | Unidad territorial base donde se ejecutan obras, capacitaciones y técnicas (RF-02, RF-05, RF-07, RF-08, RF-09). |
| Participante, beneficiario, líder comunitario | Clase `Participante` con atributo `rol` | Actores vinculados a comunidades y proyectos; se distinguen mediante su rol y datos de contacto (RF-01, RF-04, RF-08). |
| Obra de conservación de suelo y agua | Clase `ObraConservacion` | Intervención física (terrazas, zanjas) con tipo, ubicación de referencia, estado y responsable técnico (RF-02). |
| Taller, capacitación | Clase `Capacitacion` | Evento formativo con tema, fecha, localidad, número de asistentes y facilitador (RF-03, RF-05). |
| Técnica agroecológica, resultado | Clase `TecnicaAgroecologica` | Práctica aplicada en una comunidad con fecha y resultado cualitativo o cuantitativo (RF-07). |
| Zona de recarga hídrica | Atributo / Clase `ZonaRecargaHidrica` | En el modelo núcleo se sintetiza en `zonaRecargaHidrica` y `referenciaMapa` dentro de `Comunidad` (RF-09). |
| Reporte de avance, indicadores | Operación `exportarReporte()` | Consolidación calculada de avances y beneficiarios por proyecto o comunidad en PDF/CSV (RF-06). |
| Fecha, tipo, estado, ubicación, contacto | Atributos tipados | Propiedades intrínsecas de sus respectivas clases; se evita crear clases artificiales como «Fecha». |
| Sistema, pantalla, SQLite, sincronización | Descartados del dominio | Corresponden a restricciones técnicas de arquitectura o interfaz (RF-10, RNF-01 a RNF-08), no a objetos del negocio. |

---

## 3. Diagrama de clases

La Figura 1 presenta el diagrama de clases UML del módulo núcleo de SIGIPA-CEDICAM elaborado con la notación estándar de Enterprise Architect. Cada clase incorpora su estereotipo de dominio, compartimiento de atributos con visibilidad privada (`-`) y tipos de datos explícitos, compartimiento de operaciones ligadas a los verbos de los requisitos funcionales, y asociaciones etiquetadas con multiplicidad en ambos extremos.

![Diagrama de clases UML - Módulo núcleo del sistema SIGIPA-CEDICAM](docs/analisis/diagrama-clases.png)

*Figura 1. Diagrama de clases UML - Módulo núcleo del sistema SIGIPA-CEDICAM (Paquete de Análisis).*

---

## 4. Enumeraciones y tipos de datos

Para garantizar consistencia semántica y evitar cadenas de texto libres en atributos con dominios acotados definidos por el DRS, se establecen los siguientes tipos enumerados y tipos estructurados de apoyo:

| Tipo / Enumeración | Valores permitidos / Estructura | Requisito asociado |
| :--- | :--- | :--- |
| `EstadoProyecto` | `planificado`, `activo`, `concluido` | RF-01, RF-06 |
| `EstadoObra` | `planificada`, `en_proceso`, `concluida` | RF-02 (Criterio 1) |
| `RolParticipante` | `beneficiario`, `lider_comunitario`, `promotor`, `facilitador` | RF-01, RF-03, RF-04, RF-08 |
| `TipoObra` | `terraza`, `zanja_infiltracion`, `bordo`, `presa_gaviones` | RF-02, Definiciones DRS |
| `Reporte` | Estructura exportable en formato PDF o CSV con métricas de avance | RF-06 (Criterio 1 y 2) |

---

## 5. Especificación detallada de clases

A continuación se detallan los atributos y operaciones de cada una de las clases representadas en la Figura 1, verificando que ningún atributo duplique una asociación existente y que cada método pertenezca a la clase que posee los datos requeridos para ejecutarlo.

### 5.1 Proyecto («Project»)
**Requisitos:** RF-01, RF-03, RF-04, RF-06. Representa cada proyecto agroecológico operado por CEDICAM en la Mixteca, articulando participantes, obras y reportes para organismos financiadores.

| Miembro | Tipo / Retorno | Descripción y trazabilidad |
| :--- | :--- | :--- |
| **Atributos** | | |
| `-id` | `Integer` | Identificador único interno del proyecto. |
| `-nombre` | `String` | Nombre oficial del proyecto agroecológico (RF-01). |
| `-descripcion` | `String` | Objetivo general y alcance operativo del proyecto. |
| `-fechaInicio` | `Date` | Fecha formal de arranque en campo. |
| `-fechaFin` | `Date` | Fecha programada o real de cierre del proyecto. |
| `-estado` | `String` | Estado operativo vigente (`EstadoProyecto`). |
| **Operaciones** | | |
| `+getProgresos()` | `List` | Recupera el avance consolidado de las obras vinculadas (RF-02, RF-06). |
| `+registrar()` | `void` | Persiste un nuevo proyecto en almacenamiento local SQLite (RNF-01). |
| `+finalizar()` | `void` | Cambia el estado del proyecto a concluido tras validar entregables. |
| `+exportarReporte()` | `Reporte` | Genera el reporte exportable (PDF/CSV) con indicadores para donantes (RF-06). |

### 5.2 Comunidad («Localidad»)
**Requisitos:** RF-02, RF-05, RF-07, RF-08, RF-09. Representa la localidad rural de la Mixteca donde residen los participantes y se implementan obras y zonas de recarga hídrica.

| Miembro | Tipo / Retorno | Descripción y trazabilidad |
| :--- | :--- | :--- |
| **Atributos** | | |
| `-id` | `Integer` | Identificador único de la comunidad o localidad. |
| `-nombre` | `String` | Nombre oficial de la comunidad (RF-01, RF-05). |
| `-municipio` | `String` | Municipio de la región Mixteca al que pertenece. |
| `-zonaRecargaHidrica` | `String` | Descripción geográfica o paraje de recarga hídrica identificada (RF-09). |
| `-referenciaMapa` | `String` | Coordenadas o ruta local de imagen ligera de referencia hídrica (RF-09). |
| **Operaciones** | | |
| `+actualizar()` | `void` | Actualiza datos territoriales y referencias hídricas en modo *offline* (RF-09). |
| `+obtenerHistorial()` | `List` | Consulta consolidada de capacitaciones, obras y técnicas en $\le 2$ clics (RF-05, RNF-07). |

### 5.3 Participante («Beneficiario»)
**Requisitos:** RF-01, RF-03, RF-04, RF-08. Modela a las personas registradas en el padrón centralizado de CEDICAM (beneficiarios, líderes comunitarios aliados y promotores).

| Miembro | Tipo / Retorno | Descripción y trazabilidad |
| :--- | :--- | :--- |
| **Atributos** | | |
| `-id` | `Integer` | Identificador único del participante en el padrón. |
| `-nombre` | `String` | Nombre completo del participante o líder comunitario (RF-01, RF-08). |
| `-rol` | `String` | Rol o cargo desempeñado en el proyecto o localidad (RF-01, RF-08). |
| `-contacto` | `String` | Teléfono o medio de contacto comunitario (RF-01, RF-08). |
| **Operaciones** | | |
| `-validar()` | `Boolean` | Operación auxiliar privada que verifica campos obligatorios antes de guardar (RF-01). |
| `+registrar()` | `void` | Da de alta al participante en el padrón local (RF-01, RF-04). |
| `+asociarAProyecto(p: Proyecto)` | `void` | Vincula al participante con uno o múltiples proyectos agroecológicos (RF-01, RF-04). |
| `+participarEnCapacitacion(t: Taller)` | `void` | Registra la asistencia y participación en talleres impartidos (RF-03). |

### 5.4 ObraConservacion («CivilWork»)
**Requisitos:** RF-02, RF-06. Registra las intervenciones físicas de conservación de suelo y agua ejecutadas por comunidad y proyecto.

| Miembro | Tipo / Retorno | Descripción y trazabilidad |
| :--- | :--- | :--- |
| **Atributos** | | |
| `-id` | `Integer` | Identificador único de la obra de conservación. |
| `-tipo` | `String` | Tipo de obra física (terrazas, zanjas de infiltración, bordos) (RF-02). |
| `-ubicacionReferencia` | `String` | Paraje o referencia geográfica dentro de la comunidad (RF-02). |
| `-fechaRegistro` | `Date` | Fecha de captura o actualización del avance de la obra (RF-02). |
| `-estadoActual` | `String` | Estado de ejecución: planificada, en proceso o concluida (RF-02). |
| `-responsableTecnico` | `String` | Promotor o técnico responsable del seguimiento en campo (RF-02). |
| **Operaciones** | | |
| `-procesoReporte()` | `binary` | Método interno privado que serializa métricas de avance para exportación (RF-06). |
| `+crear()` | `void` | Registra una nueva obra de conservación con persistencia *offline* (RF-02). |
| `+actualizarEstado(e: String)` | `void` | Actualiza la etapa de avance de la obra (RF-02). |
| `+obtenerUbicacion()` | `String` | Devuelve la referencia espacial vinculada a la localidad (RF-02). |

---

## 6. Asociaciones y multiplicidad

Todas las relaciones entre clases cuentan con nombre descriptivo de acción y multiplicidad explícita en ambos extremos, reflejando las reglas de negocio validadas con CEDICAM. Los vínculos estructurales (como la comunidad donde reside un participante o el proyecto al que pertenece una obra) se modelan exclusivamente como asociaciones y nunca se duplican como claves foráneas primitivas dentro de los atributos.

| Clase A | Asociación | Clase B | Mult. A | Mult. B | Regla de negocio (DRS) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `Comunidad` | `ubica_en` | `Proyecto` | `1` | `0..*` | Una comunidad puede albergar cero o muchos proyectos agroecológicos. |
| `Comunidad` | `pertenece_a` | `Participante` | `1` | `0..*` | Cada participante pertenece a una comunidad base; una comunidad tiene múltiples participantes (RF-01, RF-04). |
| `Proyecto` | `integra` | `Participante` | `1..*` | `1..*` | Un participante puede asociarse a uno o más proyectos y cada proyecto integra al menos un participante (RF-01, RF-04). |
| `Proyecto` | `pertenece_a_proyecto` | `ObraConservacion` | `1` | `1..*` | Cada obra de conservación se enmarca en un proyecto y todo proyecto contempla una o más obras (RF-02). |
| `ObraConservacion` | `se_construye_en` | `Comunidad` | `0..*` | `1` | Toda obra se construye en una comunidad específica; una comunidad acumula cero o varias obras (RF-02). |

---

## 7. Encapsulamiento y visibilidad

El modelo aplica estrictamente el principio de ocultamiento de información de la orientación a objetos, cumpliendo con el Criterio 4 de la rúbrica:

* **Atributos privados (`-`):** El 100% de los atributos en todas las clases (`-id`, `-nombre`, `-estadoActual`, `-zonaRecargaHidrica`, etc.) están declarados como privados para proteger la integridad de los registros locales (RNF-05) y evitar modificaciones directas sin validación.
* **Operaciones públicas (`+`):** Las operaciones de negocio invocadas desde los casos de uso y flujos de usuario (`+registrar()`, `+exportarReporte()`, `+obtenerHistorial()`, `+actualizarEstado()`) son públicas.
* **Operaciones auxiliares privadas (`-`):** Se justifican explícitamente dos métodos privados internos: `-validar()` en `Participante` (ejecuta reglas internas de integridad antes de confirmar el alta) y `-procesoReporte()` en `ObraConservacion` (empaqueta datos binarios internos consumidos por `exportarReporte()`).
* **Visibilidad protegida (`#`):** No se emplea debido a que el modelo no utiliza jerarquías de herencia.

---

## 8. Agregación, composición y herencia

**Justificación técnica del modelo (Criterio 6):** Se optó por asociaciones simples con multiplicidad explícita y estereotipos de rol, prescindiendo de agregación, composición y herencia en el núcleo del dominio por las siguientes razones verificadas con el DRS:

* **Ausencia de composición y agregación en el núcleo:** Tanto `Participante` como `Comunidad` y `ObraConservacion` conservan existencia histórica independiente en la memoria institucional de CEDICAM. Si un proyecto concluye o se reestructura, las comunidades, los beneficiarios y las obras físicas construidas en campo siguen existiendo en el padrón histórico (RF-04, RF-07). Forzar una composición implicaría eliminar registros históricos al cerrar un proyecto.
* **Ausencia de herencia:** Los actores comunitarios (beneficiarios, líderes comunitarios y promotores) comparten la misma estructura de datos de identificación, comunidad y contacto (RF-01, RF-08). Modelarlos como subclases separadas duplicaría registros cuando un mismo beneficiario asume el cargo de líder comunitario; por ello se modela una sola clase `Participante` diferenciada por su atributo `rol`.

---

## 9. Trazabilidad con el DRS y el BPMN

Cada elemento del modelo mantiene trazabilidad bidireccional entre los requisitos del DRS v1.0, las preguntas de la entrevista de elicitación (Práctica 7), los procesos de negocio BPMN de la Unidad 2 y el archivo `docs/analisis/trazabilidad.csv`:

| Elemento UML | Tipo | Requisitos DRS | Origen P7 | Proceso BPMN | Responsabilidad funcional |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `Proyecto` | Clase | RF-01, RF-03, RF-04, RF-06 | Preguntas 5, 6 | Gestión de proyectos agroecológicos | Articulación de proyectos y exportación de reportes a donantes. |
| `Comunidad` | Clase | RF-02, RF-05, RF-07, RF-08, RF-09, RNF-07 | Preguntas 5, 6, 7 | Consulta histórica y territorial | Consolidación por localidad, zonas de recarga hídrica e historial. |
| `Participante` | Clase | RF-01, RF-03, RF-04, RF-08 | Preguntas 5, 6, 7 | Registro de padrón y líderes | Gestión centralizada de beneficiarios, contactos y asignación a proyectos. |
| `ObraConservacion` | Clase | RF-02, RF-06 | Preguntas 5, 6 | Conservación de suelo y agua | Seguimiento de tipo, ubicación y estado de avance de obras físicas. |
| `Capacitacion` | Clase apoyo | RF-03, RF-05 | Preguntas 5, 6, 7 | Capacitaciones comunitarias | Registro de talleres impartidos e historial cronológico por localidad. |
| `TecnicaAgroecologica` | Clase apoyo | RF-07 | Pregunta 7 | Preservación técnica | Registro de prácticas agroecológicas aplicadas y resultados obtenidos. |

---

## 10. Notación UML y uso de Enterprise Architect

* **Ubicación en el modelo:** El diagrama se construyó dentro del archivo `modelo/OpenHub-Equipo.qea` en Enterprise Architect, ubicado en el paquete *Análisis* bajo un diagrama formal de tipo `Class`.
* **Convenciones de nomenclatura:** Todas las clases siguen estrictamente `UpperCamelCase` (`Proyecto`, `Comunidad`, `Participante`, `ObraConservacion`), mientras que los atributos y operaciones siguen `lowerCamelCase` (`fechaInicio`, `zonaRecargaHidrica`, `ubicacionReferencia`, `exportarReporte()`).
* **Legibilidad y exportación:** Los conectores se trazaron de forma ortogonal evitando cruces innecesarios, acompañados de leyenda de visibilidad y notas de trazabilidad hacia los RF del DRS, exportando la imagen a `docs/analisis/diagrama-clases.png` con resolución superior a 800 px de ancho.

---

## 11. Trabajo por pull request y revisión cruzada

### 11.1 Flujo de trabajo colaborativo (eduScrum)
Para evitar conflictos binarios en el archivo `.qea` y asegurar la participación verificable en *Insights > Contributors*, el equipo trabajó por turnos mediante ramas temáticas y *pull requests* aprobados por un compañero distinto al autor:

| Integrante del equipo | Pull request como autor | Pull request como revisor |
| :--- | :--- | :--- |
| **Evelyn Sosa José** | PR #1 - Clases `Proyecto` y `Comunidad` | PR #2 - Revisión de `Participante` y multiplicidades |
| **Irwing Sael Feria Caballero** | PR #2 - Clase `Participante` y asociaciones | PR #3 - Revisión de `ObraConservacion` |
| **Ebenezer Acevedo Nicolas** | PR #3 - Clase `ObraConservacion` y encapsulamiento | PR #4 - Revisión de `trazabilidad.csv` y PNG |
| **Jazmin Aguilar Rojas** | PR #4 - Matriz `trazabilidad.csv` y exportación PNG | PR #1 - Revisión de clases base y notas RF |

### 11.2 Registro de la revisión cruzada
El modelo fue evaluado por un equipo revisor par utilizando la rúbrica oficial de la Práctica 9 antes de la integración final a la rama principal:

| Observación del equipo revisor | Estado / Respuesta | Qué cambió y por qué |
| :--- | :--- | :--- |
| Verificar que la multiplicidad entre `Proyecto` y `Participante` permita participación en múltiples proyectos según RF-01 y RF-04. | Corregido en PR | Se estableció multiplicidad `1..*` a `1..*` en la asociación `integra` para reflejar que un beneficiario participa en uno o varios proyectos. |
| Documentar la justificación escrita de por qué no se empleó herencia ni composición en el núcleo del modelo (Criterio 6). | Justificado y documentado | Se agregó la justificación explícita en la Sección 8 y en el README del repositorio explicando la independencia histórica de comunidades, obras y beneficiarios. |
| Confirmar visibilidad de métodos internos auxiliares (`-validar()` y `-procesoReporte()`). | Justificado en PR | Se documentó en la Sección 7 que son rutinas internas de validación y serialización invocadas únicamente dentro de su propia clase. |

---

## 12. Verificación contra la rúbrica

Matriz de autoevaluación y localización de evidencias respecto a los 10 criterios de la rúbrica de la Práctica 9 (30 puntos):

| Criterio de evaluación | Evidencia en el documento y repositorio |
| :--- | :--- |
| **1. Clases identificadas desde el DRS** | Sección 2: carrera de sustantivos depurada sobre el DRS v1.0 de SIGIPA-CEDICAM; 4 clases núcleo sin clases inventadas ni «Sistema». |
| **2. Atributos y tipos de datos** | Sección 5: atributos completos con tipos coherentes (`Integer`, `String`, `Date`, `binary`) sin duplicar asociaciones como atributos ID. |
| **3. Operaciones ligadas a los requisitos** | Sección 5 y Figura 1: operaciones derivadas de los verbos de RF-01 a RF-09 ubicadas en la clase poseedora de los datos. |
| **4. Encapsulamiento y visibilidad** | Sección 7 y Figura 1: todos los atributos privados (`-`), operaciones de negocio públicas (`+`) y excepciones internas justificadas. |
| **5. Asociaciones y multiplicidad** | Sección 6 y Figura 1: 5 asociaciones nombradas con multiplicidad en ambos extremos acordes a las reglas de CEDICAM. |
| **6. Agregación, composición y herencia** | Sección 8: justificación técnica documentada de la elección de asociaciones simples frente a composición y herencia. |
| **7. Trazabilidad con el DRS y el BPMN** | Sección 9 y archivo `docs/analisis/trazabilidad.csv` vinculando cada clase con sus RF, RNF y procesos BPMN. |
| **8. Notación UML y uso de Architect** | Secciones 3 y 10: diagrama de tipo *Class* en `modelo/OpenHub-Equipo.qea`, nomenclatura CamelCase y exportación PNG legible. |
| **9. Trabajo por pull request** | Sección 11.1: registro de autoría y revisión cruzada por turnos de los cuatro integrantes del equipo. |
| **10. Revisión cruzada y mejora** | Sección 11.2: bitácora de observaciones recibidas, respuestas en PR y ajustes aplicados al modelo. |

---

## 13. Conclusión

La elaboración del diagrama de clases UML permitió transformar los requisitos validados del DRS de SIGIPA-CEDICAM en un modelo de análisis estructurado, coherente y fiel a la operación real de los promotores y coordinadores de CEDICAM en la región Mixteca. Mediante la carrera de sustantivos se definieron las entidades esenciales (`Proyecto`, `Comunidad`, `Participante` y `ObraConservacion`), evitando redundancias y modelando las reglas del dominio mediante asociaciones con multiplicidad precisa.

Asimismo, el cumplimiento estricto de las convenciones UML en Enterprise Architect, el encapsulamiento de atributos, la matriz de trazabilidad hacia los requisitos funcionales y no funcionales (especialmente las restricciones de operación *offline*) y el flujo colaborativo mediante *pull requests* con revisión cruzada garantizan una base sólida y verificable para avanzar hacia la etapa de diseño arquitectónico y persistencia local del sistema.