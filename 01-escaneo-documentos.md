# Documentación de Operaciones SOAP de Sihot

## Operación: S_PERSON_DOCUMENT_SCAN_PUSH_V001

### Descripción
Esta operación permite a Sihot PMS iniciar un proceso de escaneo de documentos para uno o varios huéspedes. Cuando se invoca, el sistema debe responder inmediatamente para confirmar la recepción y luego iniciar el proceso de escaneo en segundo plano.

### URL del Endpoint
```
POST http://tu-api.com/v1/sihot/{id}/op
```
Donde:
- `{id}`: Es el ID de la configuración de Sihot (SihotConfig) en tu sistema

### Método HTTP
POST

### Encabezados (Headers)
```
Content-Type: text/xml; charset=utf-8
SOAPAction: "http://bindingid.com/PushNotificationsGlobal/S_PERSON_DOCUMENT_SCAN_PUSH_V001"
```

### Estructura de la Solicitud XML

#### Estructura General
```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:push="http://bindingid.com/PushNotificationsGlobal">
   <soapenv:Header/>
   <soapenv:Body>
      <push:S_PERSON_DOCUMENT_SCAN_PUSH_V001Request>
         <AUTOTASK>
            <TASK-OBJID>12345</TASK-OBJID>
            <SELECTED-SCANNER>SCANNER_ID</SELECTED-SCANNER>
            <isscanforroom>0</isscanforroom>
            <PERSON>
               <!-- Datos de la persona -->
               <RESERVATION>
                  <!-- Datos de la reserva -->
               </RESERVATION>
               <GUEST>
                  <!-- Datos del huésped -->
               </GUEST>
               <ROOMINGLIST>
                  <!-- Lista de personas adicionales -->
               </ROOMINGLIST>
            </PERSON>
         </AUTOTASK>
      </push:S_PERSON_DOCUMENT_SCAN_PUSH_V001Request>
   </soapenv:Body>
</soapenv:Envelope>
```

#### Parámetros de la Solicitud

| Parámetro | Tipo | Requerido | Descripción |
|-----------|------|-----------|-------------|
| TASK-OBJID | int | Sí | ID de la tarea en Sihot |
| SELECTED-SCANNER | string | Sí | ID del escáner seleccionado |
| isscanforroom | int | Sí | Indica si es un escaneo para toda la habitación (1) o solo para una persona (0) |

##### Estructura de PERSON
| Parámetro | Tipo | Requerido | Descripción |
|-----------|------|-----------|-------------|
| hotel | int | Sí | ID del hotel en Sihot |
| PCIID | int | Sí | ID del PCI en Sihot |
| PERSON-OBJID | int | Sí | ID del objeto persona en Sihot |
| salutation | string | No | Tratamiento (Sr., Sra., etc.) |
| title | string | No | Título (Dr., Ing., etc.) |
| name1 | string | No | Apellido del huésped |
| name2 | string | No | Nombre del huésped |
| vip | string | No | Estado VIP del huésped |
| guesttype | string | No | Tipo de huésped |
| marketsegment | string | No | Segmento de mercado |
| datefrom | date | No | Fecha de inicio |
| dateto | date | No | Fecha de fin |
| staydatefrom | date | No | Fecha de inicio de estancia |
| staydateto | date | No | Fecha de fin de estancia |
| sharedroom | int | No | Indica si la habitación es compartida |
| documenttype | string | No | Tipo de documento |
| documentnumber | string | No | Número de documento |
| language | string | No | Idioma del huésped |
| internetpassword | string | No | Contraseña de internet |

##### Estructura de RESERVATION
| Parámetro | Tipo | Requerido | Descripción |
|-----------|------|-----------|-------------|
| RESERVATION-OBJID | int | Sí | ID del objeto reserva en Sihot |
| RESERVATION-CENTRALID | int | Sí | ID central de la reserva |

##### Estructura de GUEST
| Parámetro | Tipo | Requerido | Descripción |
|-----------|------|-----------|-------------|
| GUEST-OBJID | int | Sí | ID del objeto huésped en Sihot |
| GUEST-CENTRALID | int | Sí | ID central del huésped |

##### Estructura de ROOMINGLIST
Contiene una lista de elementos PERSON con la misma estructura que el elemento PERSON principal, pero cada uno representa a un huésped adicional en la habitación.

### Ejemplo de Solicitud XML para un Solo Huésped

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:push="http://bindingid.com/PushNotificationsGlobal">
   <soapenv:Header/>
   <soapenv:Body>
      <push:S_PERSON_DOCUMENT_SCAN_PUSH_V001Request>
         <AUTOTASK>
            <TASK-OBJID>12345</TASK-OBJID>
            <SELECTED-SCANNER>SCANNER1</SELECTED-SCANNER>
            <isscanforroom>0</isscanforroom>
            <PERSON>
               <hotel>1</hotel>
               <PCIID>67890</PCIID>
               <PERSON-OBJID>54321</PERSON-OBJID>
               <salutation>Sr</salutation>
               <title></title>
               <name1>García</name1>
               <name2>Juan</name2>
               <vip></vip>
               <guesttype></guesttype>
               <marketsegment></marketsegment>
               <datefrom>2023-06-01</datefrom>
               <dateto>2023-06-05</dateto>
               <staydatefrom>2023-06-01</staydatefrom>
               <staydateto>2023-06-05</staydateto>
               <sharedroom>0</sharedroom>
               <documenttype></documenttype>
               <documentnumber></documentnumber>
               <language>ES</language>
               <internetpassword></internetpassword>
               <RESERVATION>
                  <RESERVATION-OBJID>98765</RESERVATION-OBJID>
                  <RESERVATION-CENTRALID>43210</RESERVATION-CENTRALID>
               </RESERVATION>
               <GUEST>
                  <GUEST-OBJID>12345</GUEST-OBJID>
                  <GUEST-CENTRALID>67890</GUEST-CENTRALID>
               </GUEST>
               <ROOMINGLIST>
                  <PERSON></PERSON>
               </ROOMINGLIST>
            </PERSON>
         </AUTOTASK>
      </push:S_PERSON_DOCUMENT_SCAN_PUSH_V001Request>
   </soapenv:Body>
</soapenv:Envelope>
```

### Estructura de la Respuesta XML

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Body>
      <S_PERSON_DOCUMENT_SCAN_PUSH_V001Response xmlns="http://bindingid.com/PushNotificationsGlobal">
         <S_PERSON_DOCUMENT_SCAN_PUSH_V001Result>
            <Success>true</Success>
            <ErrorMsg></ErrorMsg>
         </S_PERSON_DOCUMENT_SCAN_PUSH_V001Result>
      </S_PERSON_DOCUMENT_SCAN_PUSH_V001Response>
   </soap:Body>
</soap:Envelope>
```

#### Parámetros de la Respuesta

| Parámetro | Tipo | Descripción |
|-----------|------|-------------|
| Success | boolean | Indica si la solicitud fue recibida correctamente |
| ErrorMsg | string | Mensaje de error en caso de fallo |

### Flujo de Procesamiento

1. Sihot envía una solicitud SOAP al endpoint de tu API
2. Tu sistema identifica la configuración de Sihot correspondiente al ID en la URL
3. Tu sistema responde inmediatamente con un mensaje de éxito para confirmar la recepción
4. En segundo plano, tu sistema inicia el proceso de escaneo utilizando el escáner especificado
5. Una vez completado el escaneo, tu sistema debe enviar una confirmación a Sihot mediante la operación `S_CONFIRM_NOTIFICATION_V001`

### Notas Importantes

- La respuesta debe ser inmediata, incluso antes de iniciar el proceso de escaneo
- El proceso de escaneo debe realizarse en segundo plano
- Es importante almacenar el TASK-OBJID para utilizarlo posteriormente en la confirmación
- Si isscanforroom=1, se deben procesar todas las personas en la lista ROOMINGLIST
