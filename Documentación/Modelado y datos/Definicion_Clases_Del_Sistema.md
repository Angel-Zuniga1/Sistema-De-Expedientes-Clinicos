## Definición de clases del sistema

### *Usuario*
Representa a los usuarios del sistema (Terapeuta, Supervisor, Administrador). Los pacientes **no** son usuarios del sistema; son el sujeto de los expedientes clínicos.
+ idUsuario: Long
+ nombreCompleto: String

### Paciente (hereda de Usuario)
Posee **Expediente**
+ edad: int
+ fechaNacimiento: Date
+ correoElectronico: String
+ numeroTelefonico: String

### Terapeuta (hereda de Usuario)
Atiende a múltiples **Paciente** a través de **terapeuta_paciente** y elabora **ReporteSesion**
---
+ visualizarLista()
+ seleccionarExpediente()
+ editarExpediente()

### Supervisor (hereda de Usuario)
Supervisa a múltiples **Terapeuta** a través de **supervisor_terapeuta** y revisa **ReporteSesion**
---
+ aceptarReporte()
+ rechazarReporte()
+ reenviarReporte()
+ agregarNotaCorrectiva()
+ seleccionarReporte()

### Administrador (hereda de Usuario)
Registra **InformeConsentimiento** y **EntrevistaSocioEconomica**
---
+ anexarEntrevista()
+ anexarConsentimiento()
+ anexarExpediente()

### terapeuta_paciente
Clase de asociación N:M entre Terapeuta y Paciente.
+ idTerapeuta: Long  // FK hacia Terapeuta
+ idPaciente: Long   // FK hacia Paciente

### supervisor_terapeuta
Clase de asociación N:M entre Supervisor y Terapeuta.
+ idSupervisor: Long  // FK hacia Supervisor
+ idTerapeuta: Long   // FK hacia Terapeuta

### *Documento*
+ idDocumento: Long
+ fecha: Date
---
+ almacenar()

### Expediente
Pertenece a **Paciente** (relación 1:1). Asignado a **Terapeuta** (relación N:1).
Contiene **ReporteSesion**, **InformeConsentimiento** y **EntrevistaSocioEconomica**
+ idExpediente: Long
+ idPaciente: Long
+ idTerapeuta: Long
+ estado: EstadoExpediente  // ACTIVO | ARCHIVADO
+ fechaProxCita: DateTime

### ReporteSesion (hereda de Documento)
+ idTerapeuta: Long
+ fechaSesion: Date
+ duracionSesion: Int
+ observacionesClinicas: String
+ estado: EstadoReporte  // CREADO | PENDIENTE | APROBADO | RECHAZADO
+ comentariosTerapeuta: String
+ comentariosSupervisor: String
+ fechaCreacion: DateTime
+ fechaModificacion: DateTime

### InformeConsentimiento (hereda de Documento)
+ cuerpoDelTexto: String
+ acuerdoConfidencial: String

### EntrevistaSocioeconomica (hereda de Documento)
+ ingresoFamiliar: Decimal
+ gastoAlimentacion: Decimal
+ lugarProcedencia: String
+ vivienda: String
+ estadoSaludFamiliar: String

### RegistroAuditoria
Registro inmutable generado automáticamente por el backend tras cada operación evaluada. Solo permite operaciones de inserción; no puede ser modificado ni eliminado.
+ idLog: Long
+ idUsuario: Long
+ rolUsuario: RolUsuario      // TERAPEUTA | SUPERVISOR | ADMINISTRADOR
+ accion: AccionAuditoria     // CONSULTAR_EXPEDIENTE | MODIFICAR_EXPEDIENTE | CAMBIAR_ESTADO_EXPEDIENTE | REGISTRAR_ENTREVISTA | REGISTRAR_CONSENTIMIENTO | CONSULTAR_EXPEDIENTES_PENDIENTES | REGISTRAR_REPORTE | MODIFICAR_REPORTE | ENVIAR_REPORTE | APROBAR_REPORTE | RECHAZAR_REPORTE | CONSULTAR_TERAPEUTAS | CONSULTAR_SUPERVISORES
+ recurso: String
+ idRecurso: String
+ fechaHora: DateTime
+ resultado: ResultadoAuditoria  // PERMITIDO | DENEGADO

## Diagrama de relación de clases

![Diagrama de relación de clases](../Diagramas/Relacion_Clases.png)
