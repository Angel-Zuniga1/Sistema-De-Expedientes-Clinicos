# Diccionario de datos

### Usuario
| Nombre        | Tipo de dato  | Descripción   | Restricciones |
| ------------- |:-------------:| ------------- |:-------------:|
| idUsuario     | Long          | Número único para identificar al individuo | Único. 19 dígitos máximos y Debe existir |
| nombreCompleto| String        | Primer nombre, segundo nombre (si tiene), apellido paterno y apellido materno del usuario | Solo letras, máximo 100 caracteres y Debe existir |

### Paciente
Hereda de: Usuario
| Nombre              | Tipo de dato  | Descripción   | Restricciones |
| ------------------- |:-------------:| ------------- |:-------------:|
| edad                | int           | Edad del paciente en el momento | Edad no menor a 1 ni mayor a 120 y Debe existir |
| fechaNacimiento     | Date          | Fecha de nacimiento del paciente | Fecha no menor a 1910 ni mayor a la fecha actual |
| correoElectronico   | String        | Correo electrónico del paciente o tutor | Único. Máximo 100 caracteres y formato correo válido (usuario@dominio.com) |
| numeroTelefonico    | String        | Número telefónico del paciente o tutor | Único. Solo dígitos, 20 máximos y Debe existir |

### Terapeuta
Hereda de: Usuario

Relación N:M con Paciente mediante tabla de asociación `terapeuta_paciente`

*Sin atributos propios adicionales*

### terapeuta_paciente
Tabla de asociación entre Terapeuta y Paciente

| Nombre      | Tipo de dato  | Descripción                    | Restricciones                      |
| ----------- |:-------------:| ------------------------------ |:----------------------------------:|
| idTerapeuta | Long          | Referencia al terapeuta        | FK hacia Terapeuta. NOT NULL       |
| idPaciente  | Long          | Referencia al paciente         | FK hacia Paciente. NOT NULL        |

*Clave primaria compuesta: (idTerapeuta, idPaciente)*

### Supervisor
Hereda de: Usuario

Relación N:M con Terapeuta mediante tabla de asociación `supervisor_terapeuta`

*Sin atributos propios adicionales*

### supervisor_terapeuta
Tabla de asociación entre Supervisor y Terapeuta

| Nombre       | Tipo de dato  | Descripción                    | Restricciones                      |
| ------------ |:-------------:| ------------------------------ |:----------------------------------:|
| idSupervisor | Long          | Referencia al supervisor       | FK hacia Supervisor. NOT NULL      |
| idTerapeuta  | Long          | Referencia al terapeuta        | FK hacia Terapeuta. NOT NULL       |

*Clave primaria compuesta: (idSupervisor, idTerapeuta)*

### Administrador
Hereda de: Usuario

*Sin atributos propios adicionales*

### Documento
| Nombre      | Tipo de dato  | Descripción   | Restricciones |
| ----------- |:-------------:| ------------- |:-------------:|
| idDocumento | Long          | Número único de identificación por documento | Único. 19 dígitos máximos y Debe existir |
| fecha       | Date          | Fecha de última modificación | Fecha no mayor a la fecha actual y Debe existir |

### Expediente
Entidad independiente. Se asocia con Paciente (1:1) y con Terapeuta (N:1).

| Nombre        | Tipo de dato  | Descripción   | Restricciones |
| ------------- |:-------------:| ------------- |:-------------:|
| idExpediente  | Long          | Número único de identificación del expediente | Único. 19 dígitos máximos y Debe existir |
| idPaciente    | Long          | Referencia al paciente al que pertenece el expediente | FK hacia Paciente. Único. NOT NULL |
| idTerapeuta   | Long          | Referencia al terapeuta asignado al expediente | FK hacia Terapeuta. NOT NULL |
| estado        | ENUM          | Estado actual del expediente | Valores: `ACTIVO`, `ARCHIVADO`. Valor por defecto: `ACTIVO`. NOT NULL |
| fechaProxCita | DateTime      | Fecha de la próxima cita entre paciente y terapeuta | Fecha no menor a la fecha actual |

### ReporteSesion
Hereda de: Documento

| Nombre                | Tipo de dato  | Descripción   | Restricciones |
| --------------------- |:-------------:| ------------- |:-------------:|
| idTerapeuta           | Long          | Referencia al terapeuta que elaboró el reporte | FK hacia Terapeuta. NOT NULL |
| fechaSesion           | Date          | Fecha en que se realizó la sesión clínica | NOT NULL |
| duracionSesion        | Int           | Tiempo total que duró la sesión en minutos | Mayor a cero |
| observacionesClinicas | String        | Notas clínicas del terapeuta correspondientes a la sesión | NOT NULL |
| estado                | ENUM          | Estado del reporte en su ciclo de vida | Valores: `CREADO`, `PENDIENTE`, `APROBADO`, `RECHAZADO`. NOT NULL |
| comentariosTerapeuta  | String        | Notas adicionales del terapeuta sobre la sesión | Opcional |
| comentariosSupervisor | String        | Comentarios del supervisor al rechazar el reporte | Opcional |
| fechaCreacion         | DateTime      | Fecha y hora de creación del reporte | NOT NULL |
| fechaModificacion     | DateTime      | Fecha y hora de última modificación del reporte | NOT NULL |

### InformeConsentimiento
Hereda de: Documento

| Nombre              | Tipo de dato  | Descripción   | Restricciones |
| ------------------- |:-------------:| ------------- |:-------------:|
| cuerpoDelTexto      | String        | Texto legal completo que indica al paciente los acuerdos que deberá aceptar para recibir tratamiento | NOT NULL |
| acuerdoConfidencial | String        | Texto que contiene el deber ético y la obligación legal del personal de proteger la información privada del paciente | NOT NULL |

### EntrevistaSocioeconomica
Hereda de: Documento

| Nombre              | Tipo de dato  | Descripción   | Restricciones |
| ------------------- |:-------------:| ------------- |:-------------:|
| ingresoFamiliar     | Decimal       | Suma de ingresos de cada integrante de la familia | Valor no menor a 0 y Debe existir |
| gastoAlimentacion   | Decimal       | Parte del ingreso familiar destinado a la comida | Valor no menor a 0 y Debe existir |
| lugarProcedencia    | String        | Área geográfica de residencia del paciente | Máximo 100 caracteres y Debe existir |
| vivienda            | String        | Lugar físico donde habita el paciente y su familia, incluyendo características físicas | Máximo 1000 caracteres |
| estadoSaludFamiliar | String        | Número de enfermos crónicos en la familia al momento de la evaluación | Valor no mayor a 50 y Debe existir |

### RegistroAuditoria
Registro inmutable generado automáticamente por el backend. Solo acepta operaciones de inserción.

| Nombre      | Tipo de dato  | Descripción   | Restricciones |
| ----------- |:-------------:| ------------- |:-------------:|
| idLog       | Long          | Identificador único del registro de auditoría | Único. Autoincremental. NOT NULL |
| idUsuario   | Long          | Referencia al usuario que realizó la acción | FK hacia Usuario. NOT NULL |
| rolUsuario  | ENUM          | Rol del usuario al momento del evento | Valores: `TERAPEUTA`, `SUPERVISOR`, `ADMINISTRADOR`. NOT NULL |
| accion      | ENUM          | Tipo de operación realizada | Valores: `CONSULTAR_EXPEDIENTE`, `MODIFICAR_EXPEDIENTE`, `CAMBIAR_ESTADO_EXPEDIENTE`, `REGISTRAR_ENTREVISTA`, `REGISTRAR_CONSENTIMIENTO`, `CONSULTAR_EXPEDIENTES_PENDIENTES`, `REGISTRAR_REPORTE`, `MODIFICAR_REPORTE`, `ENVIAR_REPORTE`, `APROBAR_REPORTE`, `RECHAZAR_REPORTE`, `CONSULTAR_TERAPEUTAS`, `CONSULTAR_SUPERVISORES`. NOT NULL |
| recurso     | String        | Nombre de la entidad afectada (ej. `Expediente`, `ReporteSesion`) | NOT NULL |
| idRecurso   | String        | Identificador del recurso afectado | NOT NULL |
| fechaHora   | DateTime      | Fecha y hora del evento en UTC | Formato ISO 8601. NOT NULL |
| resultado   | ENUM          | Resultado de la operación evaluada | Valores: `PERMITIDO`, `DENEGADO`. NOT NULL |
