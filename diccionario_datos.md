# Diccionario de Datos: Módulo de Admisión e Inscripción (Refactorizado)

Este documento detalla los actores, entes, atributos y el proceso funcional del sistema de gestión escolar, actualizado con las reglas de normalización y diseño orientado a futuro.

## 1. Actores del Sistema

- **Representante:** Usuario externo (Padre, Madre o Tutor Legal) responsable de crear la cuenta base, registrar solicitudes, y formalizar pagos.
- **Administrador de Admisiones:** Personal del colegio encargado de revisar solicitudes y aprobar ingresos.

---

## 2. Entidades Principales (Modelo de Datos)

### 2.1 Entidad: Representante
Cuenta maestra que gestiona operaciones y dependientes.

**Atributos:**
- `id_representante` **(PK)**
- `cedula`, `nacionalidad`, `nombres`, `apellidos`, `fecha_nacimiento`, `sexo`, `estado_civil`
- **[ Contacto ]**
  - `correo_electronico`, `celular`, `telefono_habitacion`, `telefono_oficina`
- **[ Ubicación ]**
  - `direccion_habitacion`, `codigo_postal`, `municipio`, `estado`, `pais`
- **[ Origen ]**
  - `pais_nacimiento`, `ciudad_nacimiento`, `otras_nacionalidades`, `idiomas`, `religion`
- **[ Profesional ]**
  - `nivel_educativo`, `profesion, ocupacion`, `empresa_trabajo`, `cargo`, `direccion_trabajo`
  - `cp_trabajo, ciudad_trabajo`, `estado_trabajo, pais_trabajo`

### 2.2 Entidad: Datos_Facturacion
Perfiles impositivos bajo la titularidad del Representante.

**Atributos:**
- `id_datos_facturacion` **(PK)**
- `id_representante` **(FK)**
- `razon_social`, `rif_cedula`, `direccion_fiscal`, `correo_electronico_facturacion`

### 2.3 Entidad: Solicitud_Cupo (Staging / Puente)
Tabla transaccional de captura. Sirve exclusivamente para evaluar el ingreso. Una vez "Aceptada", sus datos migran permanentemente para no duplicar información a largo plazo.

**Atributos:**
- `id_solicitud` **(PK)**
- `id_representante` **(FK)**
- `fecha_creacion`, `estado_solicitud`
- **[ Datos del Alumno ]**
  - `nombres`, `apellidos`, `fecha_nacimiento`, `sexo`
  - `grado_a_cursar`, `colegio_procedencia`
- **[ Vínculos ]**
  - `parentesco_representante`

### 2.4 Entidad: Alumno (Maestro de Estudiantes)
Entidad definitiva. Nace oficialmente de la migración de datos ("Normalización") tras la aprobación de una Solicitud y su formalización económica.

**Atributos:**
- `id_alumno` **(PK)**
- `cedula_escolar`, `cedula (Opcional)`
- **[ Heredados de Solicitud ]**
  - `nombres`, `apellidos`, `fecha_nacimiento`, `sexo`
- **[ Datos Complementarios ]**
  - `pais_nacimiento`, `ciudad_nacimiento`, `otras_nacionalidades`, `idiomas`
- **[ Ubicación ]**
  - `direccion`, `codigo_postal`, `ciudad`, `municipio`, `estado`, `pais`
- **[ Contacto ]**
  - `telefono_habitacion`, `celular`

### 2.5 Entidad: Inscripcion (Histórico Escolar)
Clase transaccional que documenta el pago y el grado para un período académico específico. Un alumno tendrá una nueva fila aquí cada año.

**Atributos:**
- `id_inscripcion` **(PK)**
- `id_alumno` **(FK)**
- `id_periodo_academico`
- `grado_cursado`
- `monto_total`, `estatus_pago`, `fecha_pago_confirmado`
- `id_datos_facturacion` **(FK)**: Referencia de a nombre de quién se emite esta factura en particular.

---

## 3. Relaciones Lógicas y Cardinalidad (Arquitectura Optimizada)

1. **Representante (1) ---> (N) Solicitud_Cupo:** Gestiona múltiples prospectos.
2. **Representante (1) ---> (N) Datos_Facturacion:** Posee múltiples perfiles impositivos.
3. **Solicitud_Cupo (1) ---> (1) Alumno:** Tras la aprobación, la Solicitud actúa como semilla y "Migra sus datos" para crear la ficha permanente del Alumno.
4. **Alumno (1) ---> (N) Inscripcion:** A lo largo de su vida en la institución, el Alumno "Genera Histórico" de inscripciones, una por cada año/período académico.

> **Nota Arquitectónica sobre Alumno vs. Inscripción:**  
> En una base de datos normalizada para un ciclo escolar de múltiples años, es el **Alumno** (Entidad Maestra, que contiene datos físicos permanentes como fecha de nacimiento) quien genera múltiples **Inscripciones** (Entidad Transaccional temporal atada a un año específico). Afirmar que "La inscripción genera al alumno" crearía graves redundancias de datos cada nuevo año escolar obligando a duplicar el registro del estudiante. Por eso `Alumno (1) -> (N) Inscripciones` es el diseño estándar y eficiente.

---

## 4. Proceso Funcional de Admisión e Inscripción (Flujo Normalizado)

1. **Captación:** El Representante crea su cuenta y registra sus `Datos_Facturacion`.
2. **Proceso Puente (Staging):** El Representante llena una o más `Solicitud_Cupo` con los datos básicos del alumno, sin profundizar en su historia médica.
3. **Conversión y Normalización:** El colegio audita la solicitud. Al dar clic en "Aceptar", la base de datos extrae los datos biográficos de la solicitud y crea un registro maestro y definitivo en la tabla `Alumno`. La solicitud queda como sólo-lectura (histórico de ingreso).
4. **Formalización (1:N):** Con el código de Alumno generado, el Representante procede a pagar. Se inserta una fila en `Inscripcion` atando a ese `Alumno` con el Año Escolar correspondiente y el grado a cursar, generando la obligación de pago. Al confirmar el pago, la inscripción se activa.
