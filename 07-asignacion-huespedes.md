# Asignación de Huéspedes a Personas en Sihot

## Operación: S_PERSON_GUEST_ASSIGN_V001

### Descripción
Esta operación permite asignar un huésped existente a una persona en el sistema Sihot. Es útil cuando se identifica que un huésped ya existe en el sistema (mediante búsqueda) y se quiere asociar a una persona específica, por ejemplo, durante el proceso de check-in.

### URL del Endpoint
```
POST https://[sihot-instance-url]/services/PushNotificationsGlobal
```

### Método HTTP
POST

### Encabezados (Headers)
```
Content-Type: text/xml; charset=utf-8
SOAPAction: "http://www.sihot.com/schemas/PushNotificationsGlobal.xsd/S_PERSON_GUEST_ASSIGN_V001"
```

### Estructura de la Solicitud XML

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:push="http://www.sihot.com/schemas/PushNotificationsGlobal.xsd">
   <soapenv:Header/>
   <soapenv:Body>
      <push:S_PERSON_GUEST_ASSIGN_V001>
         <Authentication>
            <SecurityID>token-de-seguridad</SecurityID>
            <ValidUntilUTC>2023-06-01T12:00:00Z</ValidUntilUTC>
         </Authentication>
         <PersonSearch>
            <PERSON-OBJID>54321</PERSON-OBJID>
            <Person>
               <Guest>
                  <GUEST-OBJID>12345</GUEST-OBJID>
               </Guest>
            </Person>
         </PersonSearch>
      </push:S_PERSON_GUEST_ASSIGN_V001>
   </soapenv:Body>
</soapenv:Envelope>
```

#### Parámetros de la Solicitud

| Parámetro | Tipo | Requerido | Descripción |
|-----------|------|-----------|-------------|
| SecurityID | string | Sí | Token de seguridad obtenido mediante autenticación previa |
| ValidUntilUTC | datetime | Sí | Fecha y hora de expiración del token de seguridad |
| PERSON-OBJID | int | Sí | ID del objeto persona en Sihot |
| GUEST-OBJID | int | Sí | ID del objeto huésped en Sihot |

### Ejemplo de Solicitud XML Completa

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:push="http://www.sihot.com/schemas/PushNotificationsGlobal.xsd">
   <soapenv:Header/>
   <soapenv:Body>
      <push:S_PERSON_GUEST_ASSIGN_V001>
         <Authentication>
            <SecurityID>a1b2c3d4-e5f6-g7h8-i9j0-k1l2m3n4o5p6</SecurityID>
            <ValidUntilUTC>2023-06-01T12:00:00Z</ValidUntilUTC>
         </Authentication>
         <PersonSearch>
            <PERSON-OBJID>54321</PERSON-OBJID>
            <Person>
               <Guest>
                  <GUEST-OBJID>12345</GUEST-OBJID>
               </Guest>
            </Person>
         </PersonSearch>
      </push:S_PERSON_GUEST_ASSIGN_V001>
   </soapenv:Body>
</soapenv:Envelope>
```

### Estructura de la Respuesta XML

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Body>
      <S_PERSON_GUEST_ASSIGN_V001Response xmlns="http://www.sihot.com/schemas/PushNotificationsGlobal.xsd">
         <Result>
            <Success>true</Success>
            <ErrorMsg></ErrorMsg>
            <MSG-LIST></MSG-LIST>
         </Result>
      </S_PERSON_GUEST_ASSIGN_V001Response>
   </soap:Body>
</soap:Envelope>
```

#### Parámetros de la Respuesta

| Parámetro | Tipo | Descripción |
|-----------|------|-------------|
| Success | string | "true" si la asignación fue exitosa, "false" en caso contrario |
| ErrorMsg | string | Mensaje de error en caso de fallo |
| MSG-LIST | complex | Lista de mensajes adicionales (generalmente vacía en caso de éxito) |

### Flujo de Procesamiento

1. Tu sistema obtiene un token de autenticación válido mediante la operación S_AUTHENTICATE_V003
2. Tu sistema busca huéspedes existentes mediante la operación S_GUEST_SEARCH_V010
3. Si encuentra un huésped que coincide, envía una solicitud S_PERSON_GUEST_ASSIGN_V001
4. Sihot asigna el huésped a la persona
5. Sihot responde con un mensaje de éxito o error

### Ejemplo de Implementación en Postman

Para probar esta operación en Postman, sigue estos pasos:

1. Crea una nueva solicitud POST
2. Establece la URL: `https://[sihot-instance-url]/services/PushNotificationsGlobal`
3. Configura los encabezados:
   ```
   Content-Type: text/xml; charset=utf-8
   SOAPAction: "http://www.sihot.com/schemas/PushNotificationsGlobal.xsd/S_PERSON_GUEST_ASSIGN_V001"
   ```
4. En el cuerpo de la solicitud, selecciona "raw" y copia el ejemplo XML anterior
5. Reemplaza los valores de ejemplo con los datos reales de tu entorno
6. Envía la solicitud

### Código de Ejemplo en TypeScript

```typescript
async function assignPersonToGuest(
  sihotConfig: SihotConfig,
  personObjId: number,
  guestObjId: number,
): Promise<void> {
  const [soapClient, Authentication] = await prepareRequest(sihotConfig);

  const S_PERSON_GUEST_ASSIGN_V001Request: S_PERSON_GUEST_ASSIGN_V001Request = {
    Authentication,
    PersonSearch: {
      'PERSON-OBJID': personObjId,
      Person: {
        Guest: {
          'GUEST-OBJID': guestObjId,
        },
      },
    },
  };
  
  if (sihotConfig.debug_mode) {
    console.debug(JSON.stringify({ S_PERSON_GUEST_ASSIGN_V001Request }));
  }
  
  const [res] = await soapClient.S_PERSON_GUEST_ASSIGN_V001Async(
    S_PERSON_GUEST_ASSIGN_V001Request,
  );
  
  if (res.Result.Success !== 'true') {
    throw new Error(
      'Error al asignar persona a huésped: ' + res.Result.ErrorMsg,
    );
  }
}
```

### Flujo Completo de Asignación

A continuación se muestra un ejemplo de flujo completo para asignar un huésped existente a una persona:

```typescript
async function processGuestAssignment(
  sihotConfig: SihotConfig,
  personObjId: number,
  documentNumber: string,
): Promise<void> {
  // 1. Buscar huéspedes existentes con el mismo número de documento
  const sihotGuests = await findGuestEntries(
    sihotConfig,
    documentNumber,
  );
  
  // 2. Si no se encuentran huéspedes, no hay nada que asignar
  if (!sihotGuests.length) {
    console.log('No se encontraron huéspedes existentes');
    return;
  }
  
  // 3. Seleccionar el huésped más reciente (mayor ID)
  const sihotGuestId = Math.max(
    ...sihotGuests.map((guest) => guest['GUEST-OBJID']),
  );
  
  // 4. Asignar el huésped a la persona
  await assignPersonToGuest(
    sihotConfig,
    personObjId,
    sihotGuestId,
  );
  
  console.log(`Huésped ${sihotGuestId} asignado a persona ${personObjId}`);
}
```

### Casos de Uso

Esta operación es especialmente útil en los siguientes escenarios:

1. **Check-in de huéspedes recurrentes**: Cuando un huésped regresa al hotel, se puede asociar su perfil existente a la nueva reserva.
2. **Corrección de datos**: Si se identifica que una persona debería estar asociada a un perfil de huésped diferente.
3. **Fusión de perfiles**: Cuando se detectan perfiles duplicados y se quiere consolidar la información.
4. **Proceso de escaneo de documentos**: Después de escanear un documento, se puede buscar si el huésped ya existe y asignarlo a la persona actual.

### Notas Importantes

- Es recomendable verificar primero si el huésped existe mediante la operación S_GUEST_SEARCH_V010
- Si se encuentran múltiples huéspedes, se puede seleccionar el más reciente (mayor GUEST-OBJID)
- La asignación de huésped a persona es un paso importante para mantener la consistencia de los datos
- Después de la asignación, es recomendable actualizar los datos de la persona con la operación S_GO_PERSON_MODIFY_V004
