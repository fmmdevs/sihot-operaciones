# Documentación de Operaciones SOAP de Sihot PMS

## Índice de Contenidos

### Introducción
- [00 - Introducción a las Operaciones SOAP de Sihot](./00-introduccion.md)
  - Visión general del sistema
  - Conceptos básicos de SOAP
  - Flujo de trabajo típico
  - Configuración en Postman
  - Manejo de errores
  - Consideraciones de seguridad

### Operaciones de Escaneo de Documentos
- [01 - Escaneo de Documentos](./01-escaneo-documentos.md)
  - Descripción de la operación S_PERSON_DOCUMENT_SCAN_PUSH_V001
  - Estructura de la solicitud y respuesta
  - Parámetros detallados
  - Ejemplo completo
- [02 - Escaneo de Habitación Compartida](./02-escaneo-habitacion-compartida.md)
  - Variante para múltiples huéspedes
  - Consideraciones especiales
  - Ejemplo completo

### Operaciones de Gestión
- [03 - Confirmación de Notificaciones](./03-confirmacion-notificaciones.md)
  - Descripción de la operación S_CONFIRM_NOTIFICATION_V001
  - Estructura de la solicitud y respuesta
  - Flujo de procesamiento
  - Ejemplo de implementación
- [04 - Autenticación](./04-autenticacion.md)
  - Descripción de la operación S_AUTHENTICATE_V003
  - Gestión de tokens
  - Ejemplo de implementación
- [05 - Búsqueda de Huéspedes](./05-busqueda-huespedes.md)
  - Descripción de la operación S_GUEST_SEARCH_V010
  - Criterios de búsqueda
  - Procesamiento de resultados
- [06 - Modificación de Personas](./06-modificacion-personas.md)
  - Descripción de la operación S_GO_PERSON_MODIFY_V004
  - Actualización de datos personales
  - Mapeo de tipos de documento
- [07 - Asignación de Huéspedes](./07-asignacion-huespedes.md)
  - Descripción de la operación S_PERSON_GUEST_ASSIGN_V001
  - Flujo completo de asignación
  - Casos de uso

## Resumen de Operaciones

| Operación | Descripción | Documento |
|-----------|-------------|-----------|
| S_PERSON_DOCUMENT_SCAN_PUSH_V001 | Inicia un proceso de escaneo de documentos | [01](./01-escaneo-documentos.md), [02](./02-escaneo-habitacion-compartida.md) |
| S_CONFIRM_NOTIFICATION_V001 | Confirma la finalización de un proceso | [03](./03-confirmacion-notificaciones.md) |
| S_AUTHENTICATE_V003 | Obtiene un token de autenticación | [04](./04-autenticacion.md) |
| S_GUEST_SEARCH_V010 | Busca huéspedes en el sistema | [05](./05-busqueda-huespedes.md) |
| S_GO_PERSON_MODIFY_V004 | Modifica los datos de una persona | [06](./06-modificacion-personas.md) |
| S_PERSON_GUEST_ASSIGN_V001 | Asigna un huésped a una persona | [07](./07-asignacion-huespedes.md) |

## Flujo de Trabajo Típico

1. **Recepción de solicitud de escaneo**
   - Sihot envía una solicitud S_PERSON_DOCUMENT_SCAN_PUSH_V001
   - Tu sistema responde inmediatamente para confirmar la recepción

2. **Autenticación**
   - Tu sistema obtiene un token mediante S_AUTHENTICATE_V003
   - El token se utiliza para las operaciones posteriores

3. **Procesamiento del escaneo**
   - Tu sistema realiza el escaneo del documento
   - Se extraen los datos del documento

4. **Búsqueda de huéspedes existentes**
   - Tu sistema busca huéspedes con el mismo número de documento mediante S_GUEST_SEARCH_V010
   - Si encuentra coincidencias, selecciona el huésped más reciente

5. **Asignación de huésped (si existe)**
   - Si se encontró un huésped existente, se asigna a la persona mediante S_PERSON_GUEST_ASSIGN_V001

6. **Actualización de datos**
   - Tu sistema actualiza los datos de la persona con la información del documento mediante S_GO_PERSON_MODIFY_V004

7. **Confirmación**
   - Tu sistema confirma la finalización del proceso mediante S_CONFIRM_NOTIFICATION_V001

## Consideraciones Importantes

- Todas las operaciones requieren autenticación previa
- Es importante manejar adecuadamente los errores en cada paso
- La confirmación debe enviarse solo cuando todo el proceso ha finalizado correctamente
- Los tokens de autenticación tienen una validez limitada y deben renovarse cuando sea necesario
