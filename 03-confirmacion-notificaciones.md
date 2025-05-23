# Confirmación de Notificaciones en Sihot

## Operación: S_CONFIRM_NOTIFICATION_V001

### Descripción
Esta operación se utiliza para confirmar a Sihot que un proceso de escaneo de documentos ha sido completado. Debe ser invocada después de que todos los documentos hayan sido escaneados y procesados correctamente.

### URL del Endpoint
```
POST https://[sihot-instance-url]/services/PushNotificationsGlobal
```

### Método HTTP
POST

### Encabezados (Headers)
```
Content-Type: text/xml; charset=utf-8
SOAPAction: "http://www.sihot.com/schemas/PushNotificationsGlobal.xsd/S_CONFIRM_NOTIFICATION_V001"
```

### Estructura de la Solicitud XML

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:push="http://www.sihot.com/schemas/PushNotificationsGlobal.xsd">
   <soapenv:Header/>
   <soapenv:Body>
      <push:S_CONFIRM_NOTIFICATION_V001>
         <Authentication>
            <SecurityID>token-de-seguridad</SecurityID>
            <ValidUntilUTC>2023-06-01T12:00:00Z</ValidUntilUTC>
         </Authentication>
         <NOTIFICATION>
            <TASK-OBJID>12345</TASK-OBJID>
         </NOTIFICATION>
      </push:S_CONFIRM_NOTIFICATION_V001>
   </soapenv:Body>
</soapenv:Envelope>
```

#### Parámetros de la Solicitud

| Parámetro | Tipo | Requerido | Descripción |
|-----------|------|-----------|-------------|
| SecurityID | string | Sí | Token de seguridad obtenido mediante autenticación previa |
| ValidUntilUTC | datetime | Sí | Fecha y hora de expiración del token de seguridad |
| TASK-OBJID | int | Sí | ID de la tarea que se está confirmando (el mismo recibido en la solicitud de escaneo) |

### Ejemplo de Solicitud XML Completa

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:push="http://www.sihot.com/schemas/PushNotificationsGlobal.xsd">
   <soapenv:Header/>
   <soapenv:Body>
      <push:S_CONFIRM_NOTIFICATION_V001>
         <Authentication>
            <SecurityID>a1b2c3d4-e5f6-g7h8-i9j0-k1l2m3n4o5p6</SecurityID>
            <ValidUntilUTC>2023-06-01T12:00:00Z</ValidUntilUTC>
         </Authentication>
         <NOTIFICATION>
            <TASK-OBJID>12345</TASK-OBJID>
         </NOTIFICATION>
      </push:S_CONFIRM_NOTIFICATION_V001>
   </soapenv:Body>
</soapenv:Envelope>
```

### Estructura de la Respuesta XML

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Body>
      <S_CONFIRM_NOTIFICATION_V001Response xmlns="http://www.sihot.com/schemas/PushNotificationsGlobal.xsd">
         <Result>
            <Success>true</Success>
            <ErrorMsg></ErrorMsg>
            <MSG-LIST></MSG-LIST>
         </Result>
      </S_CONFIRM_NOTIFICATION_V001Response>
   </soap:Body>
</soap:Envelope>
```

#### Parámetros de la Respuesta

| Parámetro | Tipo | Descripción |
|-----------|------|-------------|
| Success | string | "true" si la confirmación fue exitosa, "false" en caso contrario |
| ErrorMsg | string | Mensaje de error en caso de fallo |
| MSG-LIST | complex | Lista de mensajes adicionales (generalmente vacía en caso de éxito) |

### Flujo de Procesamiento

1. Tu sistema completa el proceso de escaneo de documentos
2. Tu sistema obtiene un token de autenticación válido mediante la operación S_AUTHENTICATE_V003
3. Tu sistema envía una solicitud S_CONFIRM_NOTIFICATION_V001 con el ID de tarea correspondiente
4. Sihot procesa la confirmación y actualiza el estado de la tarea
5. Sihot responde con un mensaje de éxito o error

### Ejemplo de Implementación en Postman

Para probar esta operación en Postman, sigue estos pasos:

1. Crea una nueva solicitud POST
2. Establece la URL: `https://[sihot-instance-url]/services/PushNotificationsGlobal`
3. Configura los encabezados:
   ```
   Content-Type: text/xml; charset=utf-8
   SOAPAction: "http://www.sihot.com/schemas/PushNotificationsGlobal.xsd/S_CONFIRM_NOTIFICATION_V001"
   ```
4. En el cuerpo de la solicitud, selecciona "raw" y copia el ejemplo XML anterior
5. Reemplaza los valores de ejemplo con los datos reales de tu entorno
6. Envía la solicitud

### Código de Ejemplo en TypeScript

```typescript
async function confirmNotification(
  sihotConfig: SihotConfig,
  taskObjId: number,
): Promise<void> {
  // Obtener cliente SOAP y autenticación
  const [soapClient, Authentication] = await prepareRequest(sihotConfig);

  // Preparar solicitud
  const S_CONFIRM_NOTIFICATION_V001Request = {
    Authentication,
    NOTIFICATION: {
      'TASK-OBJID': taskObjId,
    },
  };

  // Enviar solicitud
  const [res] = await soapClient.S_CONFIRM_NOTIFICATION_V001Async(
    S_CONFIRM_NOTIFICATION_V001Request,
  );

  // Verificar resultado
  if (res.Result.Success !== 'true') {
    throw new Error(
      'Error al confirmar notificación: ' + res.Result.ErrorMsg,
    );
  }
}
```

### Notas Importantes

- Es crucial mantener el ID de tarea (TASK-OBJID) recibido en la solicitud de escaneo
- La autenticación debe ser válida en el momento de enviar la confirmación
- Si la autenticación ha expirado, se debe obtener un nuevo token antes de enviar la confirmación
- La confirmación debe enviarse solo cuando todos los documentos hayan sido procesados correctamente
- Si el proceso de escaneo falla, se debe manejar adecuadamente y no enviar la confirmación
