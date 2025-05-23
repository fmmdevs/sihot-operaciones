# Escaneo de Documentos para Habitaciones Compartidas

## Operación: S_PERSON_DOCUMENT_SCAN_PUSH_V001 (Habitación Compartida)

### Descripción
Esta variante de la operación S_PERSON_DOCUMENT_SCAN_PUSH_V001 se utiliza cuando se necesita escanear documentos para múltiples huéspedes que comparten una habitación. La principal diferencia es que el parámetro `isscanforroom` se establece en `1` y la sección `ROOMINGLIST` contiene múltiples elementos `PERSON`.

### Parámetros Específicos para Habitaciones Compartidas

| Parámetro | Valor | Descripción |
|-----------|-------|-------------|
| isscanforroom | 1 | Indica que el escaneo es para toda la habitación |
| ROOMINGLIST | - | Contiene múltiples elementos PERSON, uno para cada huésped adicional |

### Ejemplo de Solicitud XML para Habitación Compartida

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:push="http://bindingid.com/PushNotificationsGlobal">
   <soapenv:Header/>
   <soapenv:Body>
      <push:S_PERSON_DOCUMENT_SCAN_PUSH_V001Request>
         <AUTOTASK>
            <TASK-OBJID>12345</TASK-OBJID>
            <SELECTED-SCANNER>SCANNER1</SELECTED-SCANNER>
            <isscanforroom>1</isscanforroom>
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
               <sharedroom>1</sharedroom>
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
                  <PERSON>
                     <hotel>1</hotel>
                     <PCIID>67891</PCIID>
                     <PERSON-OBJID>54322</PERSON-OBJID>
                     <salutation>Sra</salutation>
                     <title></title>
                     <name1>García</name1>
                     <name2>María</name2>
                     <vip></vip>
                     <guesttype></guesttype>
                     <marketsegment></marketsegment>
                     <datefrom>2023-06-01</datefrom>
                     <dateto>2023-06-05</dateto>
                     <staydatefrom>2023-06-01</staydatefrom>
                     <staydateto>2023-06-05</staydateto>
                     <sharedroom>1</sharedroom>
                     <documenttype></documenttype>
                     <documentnumber></documentnumber>
                     <language>ES</language>
                     <internetpassword></internetpassword>
                     <GUEST-OBJID>12346</GUEST-OBJID>
                     <GUEST-CENTRALID>67891</GUEST-CENTRALID>
                  </PERSON>
                  <PERSON>
                     <hotel>1</hotel>
                     <PCIID>67892</PCIID>
                     <PERSON-OBJID>54323</PERSON-OBJID>
                     <salutation>Sr</salutation>
                     <title></title>
                     <name1>García</name1>
                     <name2>Pedro</name2>
                     <vip></vip>
                     <guesttype></guesttype>
                     <marketsegment></marketsegment>
                     <datefrom>2023-06-01</datefrom>
                     <dateto>2023-06-05</dateto>
                     <staydatefrom>2023-06-01</staydatefrom>
                     <staydateto>2023-06-05</staydateto>
                     <sharedroom>1</sharedroom>
                     <documenttype></documenttype>
                     <documentnumber></documentnumber>
                     <language>ES</language>
                     <internetpassword></internetpassword>
                     <GUEST-OBJID>12347</GUEST-OBJID>
                     <GUEST-CENTRALID>67892</GUEST-CENTRALID>
                  </PERSON>
               </ROOMINGLIST>
            </PERSON>
         </AUTOTASK>
      </push:S_PERSON_DOCUMENT_SCAN_PUSH_V001Request>
   </soapenv:Body>
</soapenv:Envelope>
```

### Flujo de Procesamiento para Habitaciones Compartidas

1. Sihot envía una solicitud SOAP con `isscanforroom=1`
2. Tu sistema identifica que se trata de un escaneo para múltiples huéspedes
3. Tu sistema responde inmediatamente con un mensaje de éxito
4. En segundo plano, tu sistema inicia el proceso de escaneo para cada persona en la lista
5. Para cada persona escaneada, se debe actualizar la información correspondiente
6. Una vez completado el escaneo de todos los huéspedes, se envía una única confirmación a Sihot

### Consideraciones Especiales

- El sistema debe estar preparado para manejar múltiples escaneos secuenciales
- Es importante mantener la asociación entre cada documento escaneado y la persona correspondiente
- La confirmación a Sihot debe enviarse solo cuando se hayan completado todos los escaneos
- Si alguno de los escaneos falla, se debe manejar adecuadamente y registrar el error

### Ejemplo de Implementación en Postman

Para probar esta operación en Postman, sigue estos pasos:

1. Crea una nueva solicitud POST
2. Establece la URL: `http://tu-api.com/v1/sihot/{id}/op`
3. Configura los encabezados:
   ```
   Content-Type: text/xml; charset=utf-8
   SOAPAction: "http://bindingid.com/PushNotificationsGlobal/S_PERSON_DOCUMENT_SCAN_PUSH_V001"
   ```
4. En el cuerpo de la solicitud, selecciona "raw" y copia el ejemplo XML anterior
5. Reemplaza los valores de ejemplo con los datos reales de tu entorno
6. Envía la solicitud

### Respuesta Esperada

La respuesta será la misma que para un escaneo individual:

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

### Notas Importantes

- La estructura de los elementos PERSON dentro de ROOMINGLIST es ligeramente diferente a la del PERSON principal
- Cada PERSON en ROOMINGLIST tiene los atributos GUEST-OBJID y GUEST-CENTRALID directamente en su estructura, no dentro de un elemento GUEST
- El sistema debe estar preparado para manejar esta diferencia estructural
- Es recomendable implementar un mecanismo de cola para procesar los escaneos secuencialmente
