# Casos de uso
## Actor: Terapeuta

### CU-01: Visualizar pacientes asignados

#### Descripción:
El terapeuta consulta la lista de pacientes que tiene asignados y que cuentan con una asignación activa en el sistema.

### Flujo principal:
1. El terapeuta accede al módulo de pacientes asignados.
2. El sistema valida que el usuario tenga rol `TERAPEUTA`.
3. El sistema localiza al terapeuta y recupera únicamente las asignaciones activas asociadas a su identificador.
4. El sistema filtra los expedientes vinculados a esas asignaciones y prepara la lista de resultados.
5. El sistema muestra por cada paciente:
    - Nombre completo del paciente
    - Número de expediente clínico
6. El terapeuta puede seleccionar un paciente desde esa lista para consultar su expediente.

### Flujo alterno:
- Si el usuario no tiene rol `TERAPEUTA`, el sistema niega el acceso al módulo.
- Si el terapeuta no tiene pacientes asignados, el sistema muestra una lista vacía sin incluir pacientes de otros terapeutas.

### Postcondiciones:
- Solo se muestran pacientes asignados al terapeuta y con asignación vigente.

### RF relacionados:
- RF-01
---
### CU-02: Acceder a expediente clínico

### Descripción:
El terapeuta consulta el expediente de un paciente asignado y visualiza su información general, sus documentos y el control de sesiones.

### Flujo principal:
1. El terapeuta selecciona un paciente desde la lista de pacientes asignados o ingresa el identificador del expediente al que desea acceder.
2. El sistema valida que el usuario tenga rol `TERAPEUTA`.
3. El sistema verifica que el expediente exista y que el terapeuta tenga relación de acceso con ese expediente.
4. Si la validación es correcta, el sistema registra el acceso en auditoría como operación permitida.
5. El sistema muestra el expediente con sus apartados principales:
    - Datos básicos del paciente
    - Estado del expediente
    - Próxima cita, si existe
    - Entrevista socioeconómica, si ya fue registrada
    - Consentimiento informado, si ya fue registrado
    - Lista de reportes de sesión asociados al expediente
6. El terapeuta puede continuar al detalle completo del expediente para revisar la información extendida de los documentos.

### Flujo alterno:
- Si el expediente no existe, el sistema registra el intento en auditoría con resultado `DENEGADO` y devuelve un mensaje de recurso no encontrado.
- Si el expediente existe pero no pertenece al terapeuta, el sistema registra el intento en auditoría con resultado `DENEGADO` y devuelve acceso denegado.
- Si el usuario no tiene rol `TERAPEUTA`, el sistema impide el acceso al expediente.

### Postcondiciones:
- El expediente solo se muestra cuando existe relación válida entre terapeuta y expediente.

### RF relacionados:
- RF-02
---

### CU-03: Registrar reporte de sesión

### Descripción:
El terapeuta registra un reporte de sesión dentro del expediente de un paciente asignado.

### Flujo principal:
1. El terapeuta abre el expediente de un paciente asignado.
2. El sistema valida que el expediente esté asociado al terapeuta.
3. El terapeuta accede al apartado de control de sesiones y selecciona la opción de nuevo reporte.
4. El sistema muestra el formulario de captura con al menos:
    - Fecha de la sesión
    - Duración de la sesión
    - Observaciones clínicas
    - Comentarios del terapeuta, si desea agregarlos
5. El terapeuta completa la información y guarda el reporte.
6. El sistema valida que los campos obligatorios no estén vacíos y que los valores tengan formato correcto.
7. El sistema verifica que el expediente no esté archivado.
8. El sistema almacena el reporte con estado inicial `CREADO`.
9. El sistema registra la operación en auditoría.

### Flujo alterno:
- Si el terapeuta intenta guardar un reporte con campos obligatorios vacíos, el sistema rechaza el guardado y solicita completar la información.
- Si el expediente no existe o no está asignado al terapeuta, el sistema deniega el registro.
- Si el expediente está archivado, el sistema impide crear nuevos reportes en ese expediente.

### Postcondiciones:
- El reporte queda almacenado en estado `CREADO` y asociado al expediente y al terapeuta.

### RF relacionados:
- RF-03
---

### CU-04: Enviar reporte a revisión

### Descripción:
El terapeuta envía un reporte propio al supervisor correspondiente para su revisión.

### Flujo principal:
1. El terapeuta accede a su lista de reportes.
2. El sistema localiza el reporte seleccionado y valida que su propietario sea el terapeuta.
3. El sistema verifica que el reporte se encuentre en un estado editable, es decir, `CREADO` o `RECHAZADO`.
4. El terapeuta selecciona la opción de enviar a revisión.
5. El sistema cambia el estado del reporte a `PENDIENTE`.
6. El sistema guarda la modificación y registra la acción en auditoría.
7. El reporte queda disponible para la bandeja de revisión del supervisor que administra al terapeuta.

### Flujo alterno:
- Si el reporte no existe, el sistema informa que el recurso no fue encontrado.
- Si el reporte no pertenece al terapeuta, el sistema deniega la operación.
- Si el reporte ya está en un estado distinto de `CREADO` o `RECHAZADO`, el sistema no permite reenviarlo.

### Postcondiciones:
- El reporte queda en estado `PENDIENTE` y listo para revisión.

### RF relacionados:
- RF-04
---

### CU-05: Modificar reporte rechazado

### Descripción:
El terapeuta corrige un reporte que previamente fue rechazado por el supervisor y lo vuelve a enviar a revisión.

### Flujo principal:
1. El terapeuta entra al listado de sus reportes.
2. El sistema filtra los reportes que pertenecen al terapeuta y que se encuentran en estado `RECHAZADO`.
3. El terapeuta selecciona uno de los reportes rechazados.
4. El sistema muestra la información actual y los comentarios del supervisor, si existen.
5. El terapeuta modifica los campos permitidos:
    - Fecha de la sesión
    - Duración de la sesión
    - Observaciones clínicas
    - Comentarios del terapeuta
6. El terapeuta guarda los cambios.
7. El sistema valida nuevamente los datos y conserva el reporte en estado `RECHAZADO` mientras el terapeuta no lo reenvíe.
8. Una vez guardadas las correcciones, el terapeuta puede reenviar el reporte a revisión.
9. El sistema actualiza el estado a `PENDIENTE` y registra la operación en auditoría.

### Flujo alterno:
- Si el reporte no está en estado `RECHAZADO`, el sistema no permite su edición.
- Si el reporte no pertenece al terapeuta, el sistema deniega el acceso.
- Si después de editar el reporte el terapeuta no guarda cambios válidos, el sistema conserva la versión anterior.

### Postcondiciones:
- El reporte rechazado queda actualizado y puede regresar a revisión en estado `PENDIENTE`.

### RF relacionados:
- RF-05

## Actor: Supervisor

Restricción: El sistema solo debe permitir acceso a reportes de terapeutas bajo la supervisión del usuario.

### CU-06: Visualizar reportes pendientes de revisión

### Descripción:
El supervisor consulta la lista de reportes en estado `PENDIENTE` enviados por los terapeutas que tiene asignados.

### Flujo principal:
1. El supervisor accede al módulo de revisión.
2. El sistema valida que el usuario tenga rol `SUPERVISOR`.
3. El supervisor puede aplicar filtros opcionales por:
    - Terapeuta
    - Rango de fechas de sesión
4. El sistema recupera únicamente los reportes en estado `PENDIENTE` de terapeutas bajo su supervisión.
5. El sistema muestra por cada reporte:
    - Identificador del reporte
    - Nombre del terapeuta
    - Fecha de la sesión
6. El supervisor puede abrir cualquier reporte listado para revisar su contenido.

### Flujo alterno:
- Si no existen reportes pendientes para el supervisor, el sistema muestra una lista vacía.
- Si el usuario no tiene rol `SUPERVISOR`, el sistema niega el acceso al módulo.
- Si el supervisor aplica filtros y no hay coincidencias, el sistema devuelve resultados vacíos sin error.

### Postcondiciones:
- Solo se muestran reportes autorizados y en estado `PENDIENTE`.

### RF relacionados:
- RF-06
---

### CU-07: Visualizar contenido de reporte

### Descripción:
El supervisor consulta el detalle completo de un reporte de sesión que pertenece a un terapeuta bajo su supervisión.

### Flujo principal:
1. El supervisor selecciona un reporte desde la lista de pendientes o busca un reporte específico por su identificador.
2. El sistema valida que el usuario tenga rol `SUPERVISOR`.
3. El sistema verifica que el reporte exista.
4. El sistema comprueba que el terapeuta autor del reporte esté bajo supervisión del usuario.
5. El sistema muestra el detalle del reporte, incluyendo:
    - Terapeuta
    - Paciente
    - Fecha de la sesión
    - Duración
    - Observaciones clínicas
    - Comentarios del terapeuta
    - Comentarios del supervisor, si existen
    - Estado actual del reporte
6. El supervisor usa esta información para decidir si aprueba o rechaza el reporte.

### Flujo alterno:
- Si el reporte no existe, el sistema devuelve un mensaje de recurso no encontrado.
- Si el reporte no pertenece a un terapeuta bajo supervisión, el sistema deniega el acceso.
- Si el usuario no tiene rol `SUPERVISOR`, el sistema no muestra el detalle del reporte.

### Postcondiciones:
- La información del reporte queda disponible en pantalla para revisión.

### RF relacionados:
- RF-07
---

### CU-08: Aprobar reporte de sesión

### Descripción:
El supervisor aprueba un reporte de sesión que ya fue revisado.

### Flujo principal:
1. El supervisor abre un reporte pendiente de revisión.
2. El sistema valida que el reporte exista y que pertenezca a un terapeuta bajo la supervisión del usuario.
3. El sistema comprueba que el reporte se encuentre en estado `PENDIENTE`.
4. El supervisor selecciona la opción `Aprobar`.
5. El sistema cambia el estado del reporte a `APROBADO`.
6. El sistema guarda la fecha de modificación y registra la acción en auditoría.
7. El reporte queda cerrado para revisión y visible como aprobado.

### Flujo alterno:
- Si el reporte no existe, el sistema informa que el recurso no fue encontrado.
- Si el reporte no pertenece a un terapeuta bajo supervisión, el sistema deniega la operación.
- Si el reporte no está en estado `PENDIENTE`, el sistema no permite aprobarlo.

### Postcondiciones:
- El reporte queda en estado `APROBADO`.

### RF relacionados:
- RF-08
---
### CU-09: Rechazar reporte de sesión

### Descripción:
El supervisor rechaza un reporte de sesión y registra comentarios para que el terapeuta realice correcciones.

### Flujo principal:
1. El supervisor abre un reporte pendiente de revisión.
2. El sistema valida que el reporte exista y que el autor esté bajo la supervisión del usuario.
3. El sistema comprueba que el reporte se encuentre en estado `PENDIENTE`.
4. El supervisor selecciona la opción `Rechazar`.
5. El sistema solicita un comentario obligatorio con la observación del rechazo.
6. El supervisor captura el comentario y confirma la operación.
7. El sistema guarda el comentario del supervisor.
8. El sistema cambia el estado del reporte a `RECHAZADO`.
9. El sistema registra la acción en auditoría para dejar trazabilidad del dictamen.

### Flujo alterno:
- Si el comentario del supervisor está vacío, el sistema impide completar el rechazo.
- Si el reporte no existe, el sistema devuelve un mensaje de recurso no encontrado.
- Si el reporte no pertenece a un terapeuta bajo supervisión o no está en estado `PENDIENTE`, el sistema rechaza la operación.

### Postcondiciones:
- El reporte queda en estado `RECHAZADO` con comentarios del supervisor disponibles para el terapeuta.

### RF relacionados:
- RF-08

---
## Actor: Administrador

### CU-10: Registrar entrevista socioeconómica

### Descripción:
Permite al administrador registrar por primera vez la información correspondiente a la entrevista socioeconómica de un paciente.

### Flujo principal:
1. El administrador accede al expediente clínico del paciente.
2. El administrador selecciona la opción de registrar entrevista socioeconómica.
3. El sistema muestra el formulario correspondiente con los campos requeridos.
4. El administrador captura la información de la entrevista.
5. El administrador guarda la información.
6. El sistema valida que el expediente exista.
7. El sistema valida los campos obligatorios y sus restricciones de formato.
8. El sistema crea el documento, lo vincula al expediente y guarda la fecha de registro.
9. El sistema confirma el alta del documento en el expediente.

### Flujo alterno:
- Si el expediente no existe, el sistema no permite registrar la entrevista.
- Si faltan campos obligatorios o los valores no cumplen el formato esperado, el sistema rechaza el guardado.

### Postcondiciones:
- La información de la entrevista socioeconómica queda almacenada en el expediente del paciente.

### RF relacionados:
- RF-10
---
### CU-11: Actualizar entrevista socioeconómica

### Descripción:
Permite al administrador modificar una entrevista socioeconómica ya existente dentro de un expediente.

### Flujo principal:
1. El administrador abre el expediente clínico del paciente.
2. El sistema muestra si la entrevista socioeconómica ya existe.
3. El administrador selecciona la opción de editar la entrevista existente.
4. El sistema presenta el formulario con los datos actuales cargados.
5. El administrador modifica la información necesaria.
6. El administrador guarda los cambios.
7. El sistema valida que el expediente exista y que la entrevista ya esté registrada.
8. El sistema actualiza los campos de la entrevista.
9. El sistema registra la modificación en auditoría.

### Flujo alterno:
- Si el expediente no existe, el sistema no permite la actualización.
- Si el expediente no tiene entrevista registrada, el sistema informa que no existe un documento para editar.
- Si los nuevos datos son inválidos, el sistema conserva la información previa.

### Postcondiciones:
- La entrevista socioeconómica queda actualizada con la nueva información.

### RF relacionados:
- RF-10
---
### CU-12: Registrar consentimiento informado

### Descripción:
Permite al administrador registrar por primera vez la información correspondiente al consentimiento informado firmado por el paciente.

### Flujo principal:
1. El administrador accede al expediente clínico del paciente.
2. El administrador selecciona la opción de registrar consentimiento informado.
3. El sistema muestra el formulario de registro.
4. El administrador captura la información del documento firmado.
5. El administrador guarda la información.
6. El sistema valida que el expediente exista.
7. El sistema valida que los campos obligatorios del consentimiento no estén vacíos.
8. El sistema almacena el consentimiento dentro del expediente clínico del paciente.
9. El sistema confirma que el documento quedó registrado correctamente.

### Flujo alterno:
- Si el expediente no existe, el sistema no permite registrar el consentimiento.
- Si el formulario está incompleto, el sistema rechaza el guardado.

### Postcondiciones:
- El consentimiento informado queda registrado en el expediente clínico del paciente.

### RF relacionados:
- RF-11
---
### CU-13: Actualizar consentimiento informado

### Descripción:
Permite al administrador modificar un consentimiento informado ya existente dentro de un expediente.

### Flujo principal:
1. El administrador abre el expediente clínico del paciente.
2. El sistema verifica si ya existe un consentimiento informado asociado.
3. El administrador selecciona la opción de editar el consentimiento existente.
4. El sistema presenta el formulario con el contenido actual.
5. El administrador corrige o amplía el texto del consentimiento.
6. El administrador guarda los cambios.
7. El sistema valida la existencia del expediente y del documento.
8. El sistema actualiza el consentimiento con la nueva información.
9. El sistema registra la modificación en auditoría.

### Flujo alterno:
- Si el expediente no existe, la actualización no procede.
- Si no existe un consentimiento previo, el sistema informa que no hay documento para editar.
- Si los nuevos campos son inválidos, el sistema conserva el contenido anterior.

### Postcondiciones:
- El consentimiento informado queda actualizado en el expediente del paciente.

### RF relacionados:
- RF-11
---
### CU-14: Consultar expedientes clínicos y documentos pendientes

### Descripción:
Permite al administrador revisar el listado general de expedientes clínicos y detectar cuáles requieren documentos pendientes.

### Flujo principal:
1. El administrador accede al módulo de expedientes.
2. El sistema valida que el usuario tenga rol `ADMINISTRADOR`.
3. El sistema recupera el listado general de expedientes con indicadores de documentos faltantes.
4. El administrador puede navegar por toda la lista o filtrar visualmente los expedientes incompletos.
5. El sistema muestra por cada expediente:
    - Identificador del expediente
    - Nombre del paciente
    - Indicador de entrevista pendiente
    - Indicador de consentimiento pendiente
6. El administrador puede seleccionar un expediente para ver su detalle completo.

### Flujo alterno:
- Si no existen expedientes registrados, el sistema muestra una lista vacía.
- Si el usuario no tiene rol `ADMINISTRADOR`, el sistema deniega el acceso.

### Postcondiciones:
- El administrador obtiene una vista de control para identificar expedientes con documentos faltantes.

### RF relacionados:
- RF-09
- RF-10
- RF-11
---
### CU-15: Consultar terapeutas

### Descripción:
Permite al administrador consultar el listado general de terapeutas registrados en el sistema.

### Flujo principal:
1. El administrador accede al listado de usuarios del módulo administrativo.
2. El sistema valida que el usuario tenga rol `ADMINISTRADOR`.
3. El sistema recupera todos los terapeutas registrados.
4. El sistema muestra por cada terapeuta:
    - Identificador del usuario
    - Nombre completo
5. El administrador utiliza la lista para seleccionar terapeutas en otras operaciones administrativas.

### Flujo alterno:
- Si no existen terapeutas registrados, el sistema muestra una lista vacía.
- Si el usuario no tiene rol `ADMINISTRADOR`, el sistema deniega la consulta.

### Postcondiciones:
- El administrador dispone de un catálogo de terapeutas para operaciones de gestión.

### RF relacionados:
- RF-09
---
### CU-16: Consultar supervisores

### Descripción:
Permite al administrador consultar el listado general de supervisores registrados en el sistema.

### Flujo principal:
1. El administrador accede al listado de usuarios del módulo administrativo.
2. El sistema valida que el usuario tenga rol `ADMINISTRADOR`.
3. El sistema recupera todos los supervisores registrados.
4. El sistema muestra por cada supervisor:
    - Identificador del usuario
    - Nombre completo
5. El administrador utiliza la lista para consultas, asignaciones o búsquedas internas.

### Flujo alterno:
- Si no existen supervisores registrados, el sistema muestra una lista vacía.
- Si el usuario no tiene rol `ADMINISTRADOR`, el sistema deniega la consulta.

### Postcondiciones:
- El administrador dispone de un catálogo de supervisores para operaciones de gestión.

### RF relacionados:
- RF-09
---
### CU-17: Consultar registros de auditoría

### Descripción:
El administrador consulta el historial de eventos registrados por el sistema de auditoría.

### Flujo principal:
1. El administrador accede al módulo de auditoría.
2. El sistema valida que el usuario tenga rol `ADMINISTRADOR`.
3. El administrador puede aplicar de forma opcional filtros de búsqueda:
    - Identificador de usuario
    - Rango de fechas
    - Tipo de acción
    - Recurso e identificador de recurso
    - Resultado `PERMITIDO` o `DENEGADO`
4. El sistema valida el formato de los filtros proporcionados.
5. El sistema retorna la lista de registros que cumplen los criterios.
6. El sistema muestra por cada registro:
    - Identificador del log
    - Usuario y rol
    - Acción realizada
    - Recurso e identificador del recurso
    - Fecha y hora
    - Resultado
7. El administrador revisa los eventos y puede refinar la búsqueda si lo requiere.

### Flujo alterno:
- Si un usuario con rol `TERAPEUTA` o `SUPERVISOR` intenta acceder al módulo, el sistema deniega el acceso.
- Si los filtros contienen un formato inválido, el sistema rechaza la consulta.
- Si no existen coincidencias, el sistema muestra una lista vacía.

### Postcondiciones:
- Los registros mostrados son de solo lectura; no es posible modificarlos ni eliminarlos desde la interfaz.

### RF relacionados:
- RF-12
---
## Trazabilidad de casos de uso

| Caso de uso | Actor | Requisito funcional |
|-------------|-------|---------------------|
| CU-01 | Terapeuta | RF-01 |
| CU-02 | Terapeuta | RF-02 |
| CU-03 | Terapeuta | RF-03 |
| CU-04 | Terapeuta | RF-04 |
| CU-05 | Terapeuta | RF-05 |
| CU-06 | Supervisor | RF-06 |
| CU-07 | Supervisor | RF-07 |
| CU-08 | Supervisor | RF-08 |
| CU-09 | Supervisor | RF-08 |
| CU-10 | Administrador | RF-10 |
| CU-11 | Administrador | RF-10 |
| CU-12 | Administrador | RF-11 |
| CU-13 | Administrador | RF-11 |
| CU-14 | Administrador | RF-09 / RF-10 / RF-11 |
| CU-15 | Administrador | RF-09 |
| CU-16 | Administrador | RF-09 |
| CU-17 | Administrador | RF-12 |

