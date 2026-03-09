# Diccionario de Datos - Módulo de Admisión e Inscripción

**Versión:** 1.0  
**Fecha:** Marzo 2026  
**Proyecto:** Sistema de Gestión Escolar - COLEGIO LIDERA

---

## 1. Introducción

### 1.1 Propósito

Este documento tiene como propósito documentar de manera técnica y exhaustiva todas las entidades, atributos, tipos de datos, restricciones y relaciones del módulo de Admisión e Inscripción del sistema de gestión escolar.

### 1.2 Alcance

Este diccionario abarca las siguientes áreas funcionales:
- Registro y gestión de representantes (padres/tutores)
- Proceso de solicitud de cupo
- Gestión de datos de estudiantes (alumnos)
- Inscripción por período académico
- Planilla de enfermería y seguimiento médico

### 1.3 Convenciones

| Símbolo | Significado |
|---------|-------------|
| PK | Primary Key (Clave Primaria) |
| FK | Foreign Key (Clave Foránea) |
| NN | Not Null (No Nulo) |
| UQ | Unique (Único) |
| AI | Auto Increment |
| DEFAULT | Valor por defecto |

---

## 2. Actores del Sistema

| Actor | Descripción | Rol en el proceso |
|-------|-------------|-------------------|
| **Representante** | Padre, madre o tutor legal del alumn | Crea cuenta, registra solicitudes, formaliza pagos e inscripciones |
| **Administrador de Admisiones** | Personal del colegio | Revisa solicitudes, aprueba/rechaza ingresos, gestiona inscripciones |

---

## 3. Entidades y Atributos

### 3.1 Representante

Entidad maestra que gestiona operaciones y dependientes económicos.

| Campo | Tipo de Dato | Restricciones | Descripción |
|-------|--------------|---------------|-------------|
| id_representante | BIGINT UNSIGNED | PK, AI, NN | Identificador único del representante |
| cedula | VARCHAR(20) | NN, UQ | Número de cédula de identidad |
| nacionalidad | ENUM('V','E','P') | NN, DEFAULT 'V' | Nacionalidad: V=Venezolano, E=Extranjero, P=Pasaporte |
| nombres | VARCHAR(100) | NN | Nombres completos del representante |
| apellidos | VARCHAR(100) | NN | Apellidos completos del representante |
| fecha_nacimiento | DATE | NN | Fecha de nacimiento |
| sexo | ENUM('M','F') | NN | Sexo: M=Masculino, F=Femenino |
| estado_civil | ENUM('S','C','D','V','U') | NN | Estado civil: S=Soltero, C=Casado, D=Divorciado, V=Viudo, U=Unión Libre |
| correo | VARCHAR(100) | NN | Correo electrónico principal |
| celular | VARCHAR(20) | NN | Número de teléfono celular |
| tel_habitacion | VARCHAR(20) | NULL | Teléfono de habitación |
| tel_oficina | VARCHAR(20) | NULL | Teléfono de oficina |
| direccion_habitacion | TEXT | NN | Dirección de habitación completa |
| codigo_postal | VARCHAR(15) | NULL | Código postal |
| pais | VARCHAR(50) | NN, DEFAULT 'Venezuela' | País de residencia |
| estado | VARCHAR(50) | NN | Estado/Entidad federativa |
| municipio | VARCHAR(50) | NN | Municipio |
| ciudad | VARCHAR(50) | NN | Ciudad |
| pais_nacimiento | VARCHAR(50) | NN, DEFAULT 'Venezuela' | País de nacimiento |
| ciudad_nacimiento | VARCHAR(50) | NULL | Ciudad de nacimiento |
| otras_nacionalidades | VARCHAR(200) | NULL | Otras nacionalidades que posee |
| idiomas | VARCHAR(200) | NULL | Idiomas que habla |
| religion | VARCHAR(50) | NULL | Religión que profesa |
| nivel_educativo | ENUM('P','S','T','U','P','E') | NN | Nivel educativo: P=Primaria, S=Secundaria, T=Técnico, U=Universitario, E=Especialización |
| profesion | VARCHAR(100) | NULL | Profesión u oficio |
| ocupacion | VARCHAR(100) | NULL | Ocupación actual |
| empresa_trabajo | VARCHAR(150) | NULL | Nombre de la empresa donde trabaja |
| cargo | VARCHAR(100) | NULL | Cargo que desempeña |
| direccion_trabajo | TEXT | NULL | Dirección del lugar de trabajo |
| pais_trabajo | VARCHAR(50) | NULL | País del trabajo |
| estado_trabajo | VARCHAR(50) | NULL | Estado del trabajo |
| ciudad_trabajo | VARCHAR(50) | NULL | Ciudad del trabajo |

---

### 3.2 Datos_Facturacion

Perfiles impositivos bajo la titularidad del representante.

| Campo | Tipo de Dato | Restricciones | Descripción |
|-------|--------------|---------------|-------------|
| id_datos_facturacion | BIGINT UNSIGNED | PK, AI, NN | Identificador único |
| id_representante | BIGINT UNSIGNED | FK, NN | FK → Representante |
| razon_social | VARCHAR(200) | NN | Razón social o nombre completo para factura |
| rif | VARCHAR(20) | NN, UQ | RIF o Cédula fiscal (formato: J/V-XXXXXXXX-X) |
| direccion_fiscal | TEXT | NN | Dirección fiscal completa para factura |
| correo | VARCHAR(100) | NN | Correo para enviar facturas |

---

### 3.3 Solicitud_Cupo

Tabla transaccional de captura inicial. Sirve para evaluar el ingreso. Una vez "Aceptada", sus datos migran a Alumno.

| Campo | Tipo de Dato | Restricciones | Descripción |
|-------|--------------|---------------|-------------|
| id_solicitud | BIGINT UNSIGNED | PK, AI, NN | Identificador único de solicitud |
| id_representante | BIGINT UNSIGNED | FK, NN | FK → Representante |
| fecha_creacion | DATETIME | NN, DEFAULT CURRENT_TIMESTAMP | Fecha y hora de creación de la solicitud |
| estado_solicitud | ENUM('P','A','R','C') | NN, DEFAULT 'P' | Estado: P=Pendiente, A=Aprobada, R=Rechazada, C=Cancelada |
| nombres | VARCHAR(100) | NN | Nombres del alumno solicitado |
| apellidos | VARCHAR(100) | NN | Apellidos del alumno solicitado |
| fecha_nacimiento | DATE | NN | Fecha de nacimiento del alumno |
| sexo | ENUM('M','F') | NN | Sexo del alumno |
| grado_a_cursar | VARCHAR(20) | NN | Grado que desea cursar (ej: 1er, 2do, 3er...) |
| año_escolar | VARCHAR(9) | NN | Año escolar (formato: 2025-2026) |
| colegio_precedencia | VARCHAR(200) | NULL | Nombre del colegio de procedencia |
| parentesco_representante | ENUM('P','M','T','A','O') | NN | Parentesco: P=Padre, M=Madre, T=Tutor, A=Abuelo, O=Otro |
| fuente_info | VARCHAR(100) | NULL | Fuente de información: quién recomendó el colegio |
| expectativa_1 | VARCHAR(100) | NULL | Primera expectativa del colegio (ej: Excelencia académica) |
| expectativa_2 | VARCHAR(100) | NULL | Segunda expectativa del colegio (ej: Bilingüismo) |
| expectativa_3 | VARCHAR(100) | NULL | Tercera expectativa del colegio (ej: Costo accesible) |

---

### 3.4 Alumno

Entidad maestra definitiva del estudiante. Nace de la migración de datos tras la aprobación de una Solicitud.

| Campo | Tipo de Dato | Restricciones | Descripción |
|-------|--------------|---------------|-------------|
| id_alumno | BIGINT UNSIGNED | PK, AI, NN | Identificador único del alumno |
| id_representante | BIGINT UNSIGNED | FK, NN | FK → Representante (padre/tutor principal) |
| cedula_escolar | VARCHAR(20) | UQ | Número de cédula escolar (opcional) |
| cedula | VARCHAR(20) | UQ | Número de cédula de identidad (si tiene, mayor de edad) |
| nombres | VARCHAR(100) | NN | Nombres completos del alumno |
| apellidos | VARCHAR(100) | NN | Apellidos completos del alumno |
| fecha_nacimiento | DATE | NN | Fecha de nacimiento |
| sexo | ENUM('M','F') | NN | Sexo: M=Masculino, F=Femenino |
| pais | VARCHAR(50) | NN, DEFAULT 'Venezuela' | País de nacimiento |
| ciudad_nacimiento | VARCHAR(50) | NULL | Ciudad de nacimiento |
| otras_nacionalidades | VARCHAR(200) | NULL | Otras nacionalidades |
| idiomas | VARCHAR(200) | NULL | Idiomas que habla |
| direccion | TEXT | NN | Dirección de residencia |
| codigo_postal | VARCHAR(15) | NULL | Código postal |
| pais_ubicacion | VARCHAR(50) | NN, DEFAULT 'Venezuela' | País de residencia |
| estado_ubicacion | VARCHAR(50) | NN | Estado de residencia |
| municipio_ubicacion | VARCHAR(50) | NN | Municipio de residencia |
| ciudad_ubicacion | VARCHAR(50) | NN | Ciudad de residencia |
| telefono_habitacion | VARCHAR(20) | NULL | Teléfono de habitación |
| telefono_celular | VARCHAR(20) | NULL | Teléfono celular |

---

### 3.5 Inscripcion

Entidad transaccional que documenta el pago y el grado para un período académico específico. Un alumno tendrá una nueva fila aquí cada año.

| Campo | Tipo de Dato | Restricciones | Descripción |
|-------|--------------|---------------|-------------|
| id_inscripcion | BIGINT UNSIGNED | PK, AI, NN | Identificador único de inscripción |
| id_alumno | BIGINT UNSIGNED | FK, NN | FK → Alumno |
| id_datos_facturacion | BIGINT UNSIGNED | FK, NN | FK → Datos_Facturacion (para emitir factura) |
| periodo_academico | VARCHAR(9) | NN | Período escolar (formato: 2025-2026) |
| grado_cursado | VARCHAR(20) | NN | Grado que cursa en este período |
| monto_total | DECIMAL(12,2) | NN | Monto total a pagar |
| estatus_pago | ENUM('P','C','F','R') | NN, DEFAULT 'P' | Estado: P=Pendiente, C=Confirmado, F=Fallido, R=Reembolsado |
| fecha_pago_confirmado | DATETIME | NULL | Fecha cuando se confirmó el pago |

---

### 3.6 Planilla_Enfermeria

Planilla médica de salud del alumno. Se crea una por período académico.

| Campo | Tipo de Dato | Restricciones | Descripción |
|-------|--------------|---------------|-------------|
| id_planilla_enfermeria | BIGINT UNSIGNED | PK, AI, NN | Identificador único |
| id_alumno | BIGINT UNSIGNED | FK, NN | FK → Alumno |
| fecha_elaboracion | DATE | NN | Fecha de elaboración de la planilla |
| godine | VARCHAR(20) | NN | Grado que cursa el alumno |
| año_escolar | VARCHAR(9) | NN | Año escolar |
| padecenfermedad | BOOLEAN | NN, DEFAULT FALSE | ¿Padece alguna enfermedad? |
| enfermedad_descripcion | TEXT | NULL | Descripción de la enfermedad |
| tratamiento_quirurgico | BOOLEAN | NN, DEFAULT FALSE | ¿Ha recibido tratamiento quirúrgico? |
| tratamiento_descripcion | TEXT | NULL | Descripción del tratamiento |
| alteracion_audicion | BOOLEAN | NN, DEFAULT FALSE | ¿Presenta alteración en audición? |
| alteracion_audicion_descripcion | TEXT | NULL | Descripción de la alteración |
| alteracion_vision | BOOLEAN | NN, DEFAULT FALSE | ¿Presenta alteración en visión? |
| alteracion_vision_descripcion | TEXT | NULL | Descripción de la alteración |
| dificultad_educacion_fisica | BOOLEAN | NN, DEFAULT FALSE | ¿Tiene dificultad para educación física? |
| dificultad_descripcion | TEXT | NULL | Descripción de la dificultad |
| necesidad_aprendizaje | BOOLEAN | NN, DEFAULT FALSE | ¿Tiene alguna necesidad de aprendizaje? |
| necesidad_descripcion | TEXT | NULL | Descripción de la necesidad |
| autoriza_primeros_auxilios | BOOLEAN | NN, DEFAULT FALSE | ¿Autoriza administer primeros auxilios? |
| alergias | TEXT | NULL | Alergias conocidas |
| contraindicaciones | TEXT | NULL | Contraindicaciones médicas |
| condiciones_especiales | TEXT | NULL | Condiciones especiales |
| observaciones | TEXT | NULL | Observaciones generales |
| otras_vacunas | TEXT | NULL | Otras vacunas aplicadas |

---

### 3.7 Tipo_Vacuna

Catálogo de tipos de vacunas del esquema de vacunación.

| Campo | Tipo de Dato | Restricciones | Descripción |
|-------|--------------|---------------|-------------|
| id_tipo_vacuna | SMALLINT UNSIGNED | PK, AI, NN | Identificador único |
| nombre | VARCHAR(50) | NN, UQ | Nombre de la vacuna |
| descripcion | VARCHAR(200) | NULL | Descripción o nombre completo |

**Nota:** El esquema de vacunación contiene 14 tipos de vacunas. Ver archivo `wireframe_enfermeria.html` para ver el listado completo de nombres técnicos.

---

### 3.8 Vacuna_Recibida

Registro de vacunas aplicadas al alumno.

| Campo | Tipo de Dato | Restricciones | Descripción |
|-------|--------------|---------------|-------------|
| id_vacuna_recibida | BIGINT UNSIGNED | PK, AI, NN | Identificador único |
| id_planilla_enfermeria | BIGINT UNSIGNED | FK, NN | FK → Planilla_Enfermeria |
| id_tipo_vacuna | SMALLINT UNSIGNED | FK, NN | FK → Tipo_Vacuna |
| dosis_1 | BOOLEAN | NN, DEFAULT FALSE | ¿Tiene dosis 1? |
| dosis_2 | BOOLEAN | NN, DEFAULT FALSE | ¿Tiene dosis 2? |
| dosis_3 | BOOLEAN | NN, DEFAULT FALSE | ¿Tiene dosis 3? |
| dosis_4 | BOOLEAN | NN, DEFAULT FALSE | ¿Tiene dosis 4? |
| refuerzo_1 | BOOLEAN | NN, DEFAULT FALSE | ¿Tiene refuerzo 1? |
| refuerzo_2 | BOOLEAN | NN, DEFAULT FALSE | ¿Tiene refuerzo 2? |

---

### 3.9 Medicamento_Autorizado

Medicamentos que el representante autoriza administer en caso de emergencia.

| Campo | Tipo de Dato | Restricciones | Descripción |
|-------|--------------|---------------|-------------|
| id_medicamento | BIGINT UNSIGNED | PK, AI, NN | Identificador único |
| id_planilla_enfermeria | BIGINT UNSIGNED | FK, NN | FK → Planilla_Enfermeria |
| nombre_medicamento | VARCHAR(100) | NN | Nombre del medicamento autorizado |

**Nota:** Ver archivo `wireframe_enfermeria.html` para ver el listado completo de medicamentos disponibles en el formulario (aproximadamente 15 medicamentos con checkboxes).

---

### 3.10 Contacto_Emergencia

Contactos de emergencia relacionados con el alumno.

| Campo | Tipo de Dato | Restricciones | Descripción |
|-------|--------------|---------------|-------------|
| id_contacto_emergencia | BIGINT UNSIGNED | PK, AI, NN | Identificador único |
| id_planilla_enfermeria | BIGINT UNSIGNED | FK, NN | FK → Planilla_Enfermeria |
| nombre | VARCHAR(100) | NN | Nombre completo del contacto |
| cedula | VARCHAR(20) | NULL | Número de cédula |
| nacionalidad | ENUM('V','E','P') | NN, DEFAULT 'V' | Nacionalidad |
| telefono_celular | VARCHAR(20) | NN | Teléfono celular |
| telefono_oficina | VARCHAR(20) | NULL | Teléfono de oficina |
| correo | VARCHAR(100) | NULL | Correo electrónico |
| parentesco | VARCHAR(30) | NN | Parentesco con el alumno |
| orden | TINYINT UNSIGNED | NN, DEFAULT 1 | Orden de prioridad (1-4) |

---

### 3.11 Medico_Tratante

Datos del médico que atiende al alumno.

| Campo | Tipo de Dato | Restricciones | Descripción |
|-------|--------------|---------------|-------------|
| id_medico_tratante | BIGINT UNSIGNED | PK, AI, NN | Identificador único |
| id_planilla_enfermeria | BIGINT UNSIGNED | FK, NN | FK → Planilla_Enfermeria |
| nombre | VARCHAR(150) | NN | Nombre y apellido del médico |
| telefono_celular | VARCHAR(20) | NULL | Teléfono celular |
| telefono_oficina | VARCHAR(20) | NULL | Teléfono de oficina |
| telefono_casa | VARCHAR(20) | NULL | Teléfono de casa |
| direccion | TEXT | NULL | Dirección del consultorio |

---

## 4. Catálogos

### 4.1 Tipo_Vacuna

Catálogo de vacunas del esquema de vacunación venezolano.

| ID | Nombre | Descripción |
|----|--------|-------------|
| 1 | BCG | Tuberculosis |
| 2 | Poliomielitis | Poliomielitis (OPV/IPV) |
| 3 | Pentavalente | DPT + HB + Hib |
| 4 | Rotavirus | Rotavirus |
| 5 | Neumococo | Neumococo |
| 6 | Influenza | Influenza |
| 7 | Triple Viral | SRP (Sarampión, Rubéola, Paperas) |
| 8 | Fiebre Amarilla | Fiebre Amarilla |
| 9 | Hepatitis A | Hepatitis A |
| 10 | Hepatitis B | Hepatitis B |
| 11 | Varicela | Varicela |
| 12 | Meningococo | Meningococo |
| 13 | TD | Tétanos y Difteria |
| 14 | VPH | Virus del Papiloma Humano |

### 4.2 Medicamentos

**Nota:** El listado completo de medicamentos con checkboxes se encuentra en el archivo `wireframe_enfermeria.html`. Aproximadamente 15 medicamentos comunes para primeros auxilios.

---

## 5. Relaciones y Cardinalidad

### 5.1 Diagrama de Relaciones

| Entidad 1 | Cardinalidad | Entidad 2 | Descripción |
|-----------|--------------|-----------|-------------|
| Representante | 1:N | Solicitud_Cupo | Un representante puede crear múltiples solicitudes |
| Representante | 1:N | Datos_Facturacion | Un representante puede tener múltiples perfiles de facturación |
| Representante | 1:N | Alumno | Un representante puede tener múltiples hijos/alumnos |
| Solicitud_Cupo | 1:1 | Alumno | Una solicitud aprobada genera un registro de alumno |
| Alumno | 1:N | Inscripcion | Un alumno puede tener múltiples inscripciones (una por año) |
| Alumno | 1:N | Planilla_Enfermeria | Un alumno puede tener múltiples planillas (una por año) |
| Inscripcion | N:1 | Datos_Facturacion | Cada inscripción se factura a un perfil |
| Planilla_Enfermeria | 1:N | Vacuna_Recibida | Una planilla puede tener múltiples registros de vacunas |
| Planilla_Enfermeria | 1:N | Medicamento_Autorizado | Una planilla puede tener múltiples medicamentos autorizados |
| Planilla_Enfermeria | 1:N | Contacto_Emergencia | Una planilla puede tener hasta 4 contactos de emergencia |
| Planilla_Enfermeria | 1:1 | Medico_Tratante | Una planilla tiene un médico tratante |
| Tipo_Vacuna | 1:N | Vacuna_Recibida | Un tipo de vacuna puede estar en múltiples registros |

### 5.2 Modelo Conceptual

```
                              ┌─────────────────┐
                              │  Representante  │
                              │   (1) ────── (N)│───────────┐
                              └─────────────────┘           │
                                    │                       │
              ┌─────────────────────┼───────────────────────┘
              │                     │
              ▼                     ▼
┌─────────────────────┐   ┌─────────────────┐
│   Solicitud_Cupo    │   │Datos_Facturacion│
│   (1) ────── (1)    │   │   (1) ────── (N)│
└─────────────────────┘   └─────────────────┘
          │
          ▼
    ┌───────────┐
    │  Alumno   │──────────────────────────────┐
    │ (1) ─(N)  │                              │
    └───────────┘                              │
          │                                    │
          ├───────────────────┐                │
          ▼                   ▼                ▼
┌──────────────────┐  ┌─────────────────┐   ┌────────────────┐
│   Inscripcion    │  │Planilla_Enferm. │   │   Alumno       │
│   (N)            │  │   (N)           │   │   (relación    │
└──────────────────┘  └────────┬────────┘   │    anterior)   │
                    ┌──────────┼──────────┐  ────────────────┘
                    │          │          │
                    ▼          ▼          ▼
            ┌───────────┐ ┌───────┐ ┌────────────┐
            │  Vacuna   │ │Medic. │ │  Contacto  │
            │Recibida   │ │Autoriz│ │  Emergencia│
            │  (N)      │ │ (N)   │ │    (N)     │
            └───────────┘ └───────┘ └────────────┘
                    │
                    ▼
            ┌─────────────┐
            │ Tipo_Vacuna │
            │  (Catálogo) │
            └─────────────┘
```

---

## 6. Proceso Funcional

### 6.1 Flujo de Datos

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         FLUJO DE INSCRIPCIÓN                                │
└─────────────────────────────────────────────────────────────────────────────┘

1. REPRESENTANTE SE REGISTRA
   └─> Crea cuenta en el sistema
   └─> Ingresa sus datos personales (27 campos)
   └─> Opcional: Registra datos de facturación

2. SOLICITUD DE CUPO
   └─> Representante llena Solicitud_Cupo
   └─> Incluye datos básicos del alumno (14 campos)
   └─> Queda en estado "Pendiente" (P)

3. REVISIÓN ADMINISTRATIVA
   └─> Administrador revisa la solicitud
   └─> Aprueba (A) o Rechaza (R)

4. MIGRACIÓN A ALUMNO
   └─> Si es aprobada: Se crea registro en Alumno
   └─> Los datos biográficos migran desde Solicitud_Cupo
   └─> Solicitud queda como histórico (solo lectura)

5. INSCRIPCIÓN
   └─> Representante selecciona período académico y grado
   └─> Se crea registro en Inscripcion
   └─> Se selecciona datos de facturación para factura
   └─> Queda pendiente de pago (estatus_pago = 'P')

6. PAGO
   └─> Representante realiza el pago
   └─> Sistema confirma pago
   └─> estatus_pago cambia a 'C' (Confirmado)

7. PLANILLA DE ENFERMERÍA (por período)
   └─> Representante llena Planilla_Enfermeria
   └─> Registra: salud, vacunas, medicamentos, contactos, médico
   └─> Queda asociada al alumno y período académico
```

---

## 7. Notas Técnicas

### 7.1 Consideraciones de Implementación

1. **IDs y AutoIncrement**
   - Todas las entidades usan BIGINT UNSIGNED con AUTO_INCREMENT
   - Los IDs nunca se reutilizan

2. **Enumeraciones**
   - Los campos ENUM deben sincronizarse con la aplicación
   - Cambios en ENUMs requieren migración de base de datos

3. **cardinalidad 1:1 entre Solicitud_Cupo y Alumno**
   - Se implementa sin FK en Alumno (los datos migran)
   - Para consultar: buscar por id_representante + estado = 'A'

4. **Período Académico**
   - Formato: '2025-2026' (string de 9 caracteres)
   - Útil para reportes históricos

5. **Planilla de Enfermería por Período**
   - Un alumno puede tener múltiples planillas (una por año)
   - Se identifica por: id_alumno + año_escolar

### 7.2 Índices Recomendados

```sql
-- Para búsqueda de alumnos por representante
CREATE INDEX idx_alumno_representante ON Alumno(id_representante);

-- Para búsqueda de inscripciones por período
CREATE INDEX idx_inscripcion_periodo ON Inscripcion(periodo_academico);

-- Para búsqueda de inscripciones por estado de pago
CREATE INDEX idx_inscripcion_pago ON Inscripcion(estatus_pago);

-- Para búsqueda de planillas por alumno y año
CREATE INDEX idx_enfermeria_alumno_año ON Planilla_Enfermeria(id_alumno, año_escolar);

-- Para búsqueda de solicitudes por representante
CREATE INDEX idx_solicitud_representante ON Solicitud_Cupo(id_representante);
```

### 7.3 Validaciones de Negocio

| Regla | Descripción |
|-------|-------------|
| R001 | Un representante no puede tener dos solicitudes pendientes para el mismo período |
| R002 | Un alumno no puede tener dos inscripciones para el mismo período académico |
| R003 | La inscripción solo se activa cuando estatus_pago = 'C' |
| R004 | Una planilla de enfermería debe tener al menos un contacto de emergencia |

---

## 8. Anexos

### 8.1 Glosario de Términos

| Término | Definición |
|---------|------------|
| PK | Clave primaria que identifica único cada registro |
| FK | Clave foránea que establece relación entre tablas |
| NN | Not Null - Campo obligatorio |
| UQ | Unique - Campo con valor único |
| AI | Auto Increment - Valor se genera automáticamente |
| ENUM | Tipo de dato con valores predefinidos |
| BIGINT | Entero grande (hasta 9.223.372.036.854.775.807) |
| DECIMAL(12,2) | Número decimal con 12 dígitos totales y 2 decimales |

### 8.2 Referencias

- Diagrama ER: `lidera.drawio`
- Wireframe Solicitud: `wireframe_solicitud.html`
- Wireframe Inscripción: `wireframe_inscripcion.html`
- Wireframe Enfermería: `wireframe_enfermeria.html`

---

**Documento elaborado por:** Equipo de Desarrollo  
**Última actualización:** Marzo 2026
