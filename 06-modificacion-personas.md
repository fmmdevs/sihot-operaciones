# Modificación de Personas en Sihot

## Operación: S_GO_PERSON_MODIFY_V004

### Descripción
Esta operación permite modificar los datos de una persona en el sistema Sihot. Es especialmente útil después de un escaneo de documentos para actualizar la información del huésped con los datos obtenidos del documento.

### URL del Endpoint
```
POST https://[sihot-instance-url]/services/PushNotificationsGlobal
```

### Método HTTP
POST

### Encabezados (Headers)
```
Content-Type: text/xml; charset=utf-8
SOAPAction: "http://www.sihot.com/schemas/PushNotificationsGlobal.xsd/S_GO_PERSON_MODIFY_V004"
```

### Estructura de la Solicitud XML

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:push="http://www.sihot.com/schemas/PushNotificationsGlobal.xsd">
   <soapenv:Header/>
   <soapenv:Body>
      <push:S_GO_PERSON_MODIFY_V004>
         <Authentication>
            <SecurityID>token-de-seguridad</SecurityID>
            <ValidUntilUTC>2023-06-01T12:00:00Z</ValidUntilUTC>
         </Authentication>
         <PERSON>
            <PERSON-OBJID>54321</PERSON-OBJID>
            <salutation>Sr</salutation>
            <title></title>
            <name1>García</name1>
            <name2>Juan</name2>
            <documenttype>DNI</documenttype>
            <documentnumber>12345678A</documentnumber>
            <birthdate>1980-01-15</birthdate>
            <nationality>ES</nationality>
            <address1>Calle Principal 123</address1>
            <address2>Piso 4B</address2>
            <zip>28001</zip>
            <city>Madrid</city>
            <country>ES</country>
            <email>juan.garcia@example.com</email>
            <phone>+34612345678</phone>
            <language>ES</language>
         </PERSON>
      </push:S_GO_PERSON_MODIFY_V004>
   </soapenv:Body>
</soapenv:Envelope>
```

#### Parámetros de la Solicitud

| Parámetro | Tipo | Requerido | Descripción |
|-----------|------|-----------|-------------|
| SecurityID | string | Sí | Token de seguridad obtenido mediante autenticación previa |
| ValidUntilUTC | datetime | Sí | Fecha y hora de expiración del token de seguridad |
| PERSON-OBJID | int | Sí | ID del objeto persona en Sihot |
| salutation | string | No | Tratamiento (Sr., Sra., etc.) |
| title | string | No | Título (Dr., Ing., etc.) |
| name1 | string | No | Apellido del huésped |
| name2 | string | No | Nombre del huésped |
| documenttype | string | No | Tipo de documento |
| documentnumber | string | No | Número de documento |
| birthdate | date | No | Fecha de nacimiento |
| nationality | string | No | Nacionalidad (código ISO) |
| address1 | string | No | Dirección línea 1 |
| address2 | string | No | Dirección línea 2 |
| zip | string | No | Código postal |
| city | string | No | Ciudad |
| country | string | No | País (código ISO) |
| email | string | No | Correo electrónico |
| phone | string | No | Teléfono |
| language | string | No | Idioma (código ISO) |

### Ejemplo de Solicitud XML Completa

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:push="http://www.sihot.com/schemas/PushNotificationsGlobal.xsd">
   <soapenv:Header/>
   <soapenv:Body>
      <push:S_GO_PERSON_MODIFY_V004>
         <Authentication>
            <SecurityID>a1b2c3d4-e5f6-g7h8-i9j0-k1l2m3n4o5p6</SecurityID>
            <ValidUntilUTC>2023-06-01T12:00:00Z</ValidUntilUTC>
         </Authentication>
         <PERSON>
            <PERSON-OBJID>54321</PERSON-OBJID>
            <salutation>Sr</salutation>
            <title></title>
            <name1>García</name1>
            <name2>Juan</name2>
            <documenttype>DNI</documenttype>
            <documentnumber>12345678A</documentnumber>
            <birthdate>1980-01-15</birthdate>
            <nationality>ES</nationality>
            <address1>Calle Principal 123</address1>
            <address2>Piso 4B</address2>
            <zip>28001</zip>
            <city>Madrid</city>
            <country>ES</country>
            <email>juan.garcia@example.com</email>
            <phone>+34612345678</phone>
            <language>ES</language>
         </PERSON>
      </push:S_GO_PERSON_MODIFY_V004>
   </soapenv:Body>
</soapenv:Envelope>
```

### Estructura de la Respuesta XML

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Body>
      <S_GO_PERSON_MODIFY_V004Response xmlns="http://www.sihot.com/schemas/PushNotificationsGlobal.xsd">
         <Result>
            <Success>true</Success>
            <ErrorMsg></ErrorMsg>
            <MSG-LIST></MSG-LIST>
         </Result>
      </S_GO_PERSON_MODIFY_V004Response>
   </soap:Body>
</soap:Envelope>
```

#### Parámetros de la Respuesta

| Parámetro | Tipo | Descripción |
|-----------|------|-------------|
| Success | string | "true" si la modificación fue exitosa, "false" en caso contrario |
| ErrorMsg | string | Mensaje de error en caso de fallo |
| MSG-LIST | complex | Lista de mensajes adicionales (generalmente vacía en caso de éxito) |

### Flujo de Procesamiento

1. Tu sistema obtiene un token de autenticación válido mediante la operación S_AUTHENTICATE_V003
2. Tu sistema envía una solicitud S_GO_PERSON_MODIFY_V004 con los datos actualizados
3. Sihot actualiza la información de la persona
4. Sihot responde con un mensaje de éxito o error

### Ejemplo de Implementación en Postman

Para probar esta operación en Postman, sigue estos pasos:

1. Crea una nueva solicitud POST
2. Establece la URL: `https://[sihot-instance-url]/services/PushNotificationsGlobal`
3. Configura los encabezados:
   ```
   Content-Type: text/xml; charset=utf-8
   SOAPAction: "http://www.sihot.com/schemas/PushNotificationsGlobal.xsd/S_GO_PERSON_MODIFY_V004"
   ```
4. En el cuerpo de la solicitud, selecciona "raw" y copia el ejemplo XML anterior
5. Reemplaza los valores de ejemplo con los datos reales de tu entorno
6. Envía la solicitud

### Código de Ejemplo en TypeScript

```typescript
class SihotPersonModifyDto {
  constructor(
    guest: ReadingResultWithDocument,
    documentTypes: Map<DocumentType, string>,
    options: { swapNames: boolean },
  ) {
    // Mapear los datos del documento escaneado a los campos de Sihot
    this.salutation = this.mapSalutation(guest.document.gender);
    
    if (options.swapNames) {
      this.name1 = guest.document.given_name;
      this.name2 = guest.document.surname;
    } else {
      this.name1 = guest.document.surname;
      this.name2 = guest.document.given_name;
    }
    
    this.documenttype = documentTypes.get(guest.document.type) || '';
    this.documentnumber = guest.document.personal_number;
    this.birthdate = guest.document.date_of_birth;
    this.nationality = guest.document.nationality;
    
    // Otros campos si están disponibles
    if (guest.document.address) {
      this.address1 = guest.document.address.street;
      this.city = guest.document.address.city;
      this.country = guest.document.nationality;
    }
  }
  
  // Campos de la persona
  salutation?: string;
  title?: string;
  name1?: string;
  name2?: string;
  documenttype?: string;
  documentnumber?: string;
  birthdate?: string;
  nationality?: string;
  address1?: string;
  address2?: string;
  zip?: string;
  city?: string;
  country?: string;
  email?: string;
  phone?: string;
  language?: string;
  
  private mapSalutation(gender?: string): string {
    if (!gender) return '';
    return gender.toUpperCase() === 'F' ? 'Sra' : 'Sr';
  }
}

async function modifyPerson(
  sihotConfig: SihotConfig,
  personObjId: number,
  personModifyDto: SihotPersonModifyDto,
): Promise<void> {
  const [soapClient, Authentication] = await prepareRequest(sihotConfig);

  const S_GO_PERSON_MODIFY_V004Request = {
    Authentication,
    PERSON: {
      'PERSON-OBJID': personObjId,
      ...personModifyDto,
    },
  };
  
  if (sihotConfig.debug_mode) {
    console.debug(JSON.stringify({ S_GO_PERSON_MODIFY_V004Request }));
  }
  
  const [res] = await soapClient.S_GO_PERSON_MODIFY_V004Async(
    S_GO_PERSON_MODIFY_V004Request,
  );
  
  if (res.Result.Success !== 'true') {
    throw new Error(
      'Error al modificar persona: ' + res.Result.ErrorMsg,
    );
  }
}
```

### Notas Importantes

- Solo se actualizarán los campos incluidos en la solicitud
- Es importante mapear correctamente los tipos de documento según la configuración de Sihot
- Algunos hoteles pueden requerir que los nombres se intercambien (nombre y apellido)
- Verifica siempre el resultado de la operación para asegurarte de que la modificación fue exitosa
