# Búsqueda de Huéspedes en Sihot

## Operación: S_GUEST_SEARCH_V010

### Descripción
Esta operación permite buscar huéspedes en el sistema Sihot utilizando diferentes criterios, como número de documento, nombre, apellido, etc. Es útil para verificar si un huésped ya existe en el sistema antes de crear uno nuevo.

### URL del Endpoint
```
POST https://[sihot-instance-url]/services/PushNotificationsGlobal
```

### Método HTTP
POST

### Encabezados (Headers)
```
Content-Type: text/xml; charset=utf-8
SOAPAction: "http://www.sihot.com/schemas/PushNotificationsGlobal.xsd/S_GUEST_SEARCH_V010"
```

### Estructura de la Solicitud XML

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:push="http://www.sihot.com/schemas/PushNotificationsGlobal.xsd">
   <soapenv:Header/>
   <soapenv:Body>
      <push:S_GUEST_SEARCH_V010>
         <Authentication>
            <SecurityID>token-de-seguridad</SecurityID>
            <ValidUntilUTC>2023-06-01T12:00:00Z</ValidUntilUTC>
         </Authentication>
         <GUESTSEARCH>
            <documentnumber>12345678A</documentnumber>
            <!-- Otros criterios de búsqueda opcionales -->
         </GUESTSEARCH>
      </push:S_GUEST_SEARCH_V010>
   </soapenv:Body>
</soapenv:Envelope>
```

#### Parámetros de la Solicitud

| Parámetro | Tipo | Requerido | Descripción |
|-----------|------|-----------|-------------|
| SecurityID | string | Sí | Token de seguridad obtenido mediante autenticación previa |
| ValidUntilUTC | datetime | Sí | Fecha y hora de expiración del token de seguridad |
| documentnumber | string | No | Número de documento del huésped |
| name1 | string | No | Apellido del huésped |
| name2 | string | No | Nombre del huésped |
| email | string | No | Correo electrónico del huésped |
| phone | string | No | Teléfono del huésped |

### Ejemplo de Solicitud XML Completa

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:push="http://www.sihot.com/schemas/PushNotificationsGlobal.xsd">
   <soapenv:Header/>
   <soapenv:Body>
      <push:S_GUEST_SEARCH_V010>
         <Authentication>
            <SecurityID>a1b2c3d4-e5f6-g7h8-i9j0-k1l2m3n4o5p6</SecurityID>
            <ValidUntilUTC>2023-06-01T12:00:00Z</ValidUntilUTC>
         </Authentication>
         <GUESTSEARCH>
            <documentnumber>12345678A</documentnumber>
         </GUESTSEARCH>
      </push:S_GUEST_SEARCH_V010>
   </soapenv:Body>
</soapenv:Envelope>
```

### Estructura de la Respuesta XML

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Body>
      <S_GUEST_SEARCH_V010Response xmlns="http://www.sihot.com/schemas/PushNotificationsGlobal.xsd">
         <Result>
            <Success>true</Success>
            <ErrorMsg></ErrorMsg>
            <MSG-LIST></MSG-LIST>
         </Result>
         <GUESTS>
            <GUEST>
               <GUEST-OBJID>12345</GUEST-OBJID>
               <GUEST-CENTRALID>67890</GUEST-CENTRALID>
               <salutation>Sr</salutation>
               <title></title>
               <name1>García</name1>
               <name2>Juan</name2>
               <documenttype>DNI</documenttype>
               <documentnumber>12345678A</documentnumber>
               <email>juan.garcia@example.com</email>
               <phone>+34612345678</phone>
               <address1>Calle Principal 123</address1>
               <address2>Piso 4B</address2>
               <zip>28001</zip>
               <city>Madrid</city>
               <country>ES</country>
               <language>ES</language>
               <birthdate>1980-01-15</birthdate>
               <nationality>ES</nationality>
               <vip></vip>
               <guesttype></guesttype>
               <marketsegment></marketsegment>
            </GUEST>
            <!-- Más resultados si existen -->
         </GUESTS>
      </S_GUEST_SEARCH_V010Response>
   </soap:Body>
</soap:Envelope>
```

#### Parámetros de la Respuesta

| Parámetro | Tipo | Descripción |
|-----------|------|-------------|
| Success | string | "true" si la búsqueda fue exitosa, "false" en caso contrario |
| ErrorMsg | string | Mensaje de error en caso de fallo |
| MSG-LIST | complex | Lista de mensajes adicionales (generalmente vacía en caso de éxito) |
| GUESTS | complex | Lista de huéspedes encontrados |
| GUEST-OBJID | int | ID del objeto huésped en Sihot |
| GUEST-CENTRALID | int | ID central del huésped |
| Otros campos | varios | Información detallada del huésped |

### Flujo de Procesamiento

1. Tu sistema obtiene un token de autenticación válido mediante la operación S_AUTHENTICATE_V003
2. Tu sistema envía una solicitud S_GUEST_SEARCH_V010 con los criterios de búsqueda
3. Sihot busca huéspedes que coincidan con los criterios
4. Sihot responde con una lista de huéspedes encontrados
5. Tu sistema procesa los resultados según sea necesario

### Ejemplo de Implementación en Postman

Para probar esta operación en Postman, sigue estos pasos:

1. Crea una nueva solicitud POST
2. Establece la URL: `https://[sihot-instance-url]/services/PushNotificationsGlobal`
3. Configura los encabezados:
   ```
   Content-Type: text/xml; charset=utf-8
   SOAPAction: "http://www.sihot.com/schemas/PushNotificationsGlobal.xsd/S_GUEST_SEARCH_V010"
   ```
4. En el cuerpo de la solicitud, selecciona "raw" y copia el ejemplo XML anterior
5. Reemplaza los valores de ejemplo con los datos reales de tu entorno
6. Envía la solicitud

### Código de Ejemplo en TypeScript

```typescript
async function findGuestEntries(
  sihotConfig: SihotConfig,
  documentnumber: string,
): Promise<SihotGuestSearchResultDto[]> {
  const [soapClient, Authentication] = await prepareRequest(sihotConfig);

  const S_GUEST_SEARCH_V010Request = {
    Authentication,
    GUESTSEARCH: {
      documentnumber,
    },
  };
  
  if (sihotConfig.debug_mode) {
    console.debug(JSON.stringify({ S_GUEST_SEARCH_V010Request }));
  }
  
  const [res]: [S_GUEST_SEARCH_V010Response] =
    await soapClient.S_GUEST_SEARCH_V010Async(S_GUEST_SEARCH_V010Request);

  if (!res.GUESTS?.GUEST.length) {
    return [];
  }

  return res.GUESTS?.GUEST.filter(
    ({ documentnumber: guestDocNumber }) => guestDocNumber === documentnumber,
  );
}
```

### Uso Práctico

Este método es especialmente útil para:

1. Verificar si un huésped ya existe en el sistema antes de crear uno nuevo
2. Obtener el ID de un huésped para asociarlo a una persona durante el proceso de check-in
3. Buscar huéspedes por diferentes criterios para resolver duplicados
4. Obtener información detallada de un huésped para mostrarla en la interfaz de usuario

### Notas Importantes

- Es recomendable filtrar los resultados para obtener coincidencias exactas
- Si se encuentran múltiples huéspedes, se puede seleccionar el más reciente (mayor GUEST-OBJID)
- La búsqueda por número de documento suele ser la más precisa
- Si no se encuentra ningún huésped, se puede proceder a crear uno nuevo
