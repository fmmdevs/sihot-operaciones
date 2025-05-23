# Introducción a las Operaciones SOAP de Sihot

## Visión General

Este conjunto de documentos proporciona una guía completa sobre las operaciones SOAP disponibles en el sistema Sihot PMS (Property Management System). Cada documento detalla una operación específica, su estructura, parámetros y ejemplos de uso.

## Estructura de la Documentación

La documentación está organizada en los siguientes archivos:

1. **00-introduccion.md** - Este archivo, que proporciona una visión general
2. **01-escaneo-documentos.md** - Documentación sobre la operación de escaneo de documentos
3. **02-escaneo-habitacion-compartida.md** - Variante para habitaciones compartidas
4. **03-confirmacion-notificaciones.md** - Cómo confirmar notificaciones a Sihot
5. **04-autenticacion.md** - Proceso de autenticación en Sihot
6. **05-busqueda-huespedes.md** - Búsqueda de huéspedes en el sistema

## Conceptos Básicos de SOAP

SOAP (Simple Object Access Protocol) es un protocolo estándar que define cómo dos objetos en diferentes procesos pueden comunicarse por medio de intercambio de datos XML. Sihot utiliza SOAP para todas sus operaciones de API.

### Estructura Básica de una Petición SOAP

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:push="http://www.sihot.com/schemas/PushNotificationsGlobal.xsd">
   <soapenv:Header/>
   <soapenv:Body>
      <push:NombreOperacion>
         <!-- Parámetros de la operación -->
      </push:NombreOperacion>
   </soapenv:Body>
</soapenv:Envelope>
```

### Estructura Básica de una Respuesta SOAP

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Body>
      <NombreOperacionResponse xmlns="http://www.sihot.com/schemas/PushNotificationsGlobal.xsd">
         <Result>
            <Success>true</Success>
            <ErrorMsg></ErrorMsg>
            <MSG-LIST></MSG-LIST>
         </Result>
         <!-- Datos de respuesta específicos de la operación -->
      </NombreOperacionResponse>
   </soap:Body>
</soap:Envelope>
```

## Flujo de Trabajo Típico

El flujo de trabajo típico para interactuar con Sihot incluye los siguientes pasos:

1. **Autenticación** - Obtener un token de seguridad mediante S_AUTHENTICATE_V003
2. **Operación Principal** - Realizar la operación deseada (búsqueda, escaneo, etc.)
3. **Confirmación** - Si es necesario, confirmar la operación mediante S_CONFIRM_NOTIFICATION_V001

## Configuración en Postman

Para probar estas operaciones en Postman, sigue estos pasos generales:

1. Crea una nueva colección para Sihot
2. Configura variables de entorno para:
   - URL base de Sihot
   - Credenciales de autenticación
   - ID de hotel
   - Otros parámetros comunes
3. Crea una solicitud para cada operación
4. Configura los encabezados y el cuerpo XML según la documentación
5. Utiliza los ejemplos proporcionados como punto de partida

### Ejemplo de Variables de Entorno

| Variable | Valor | Descripción |
|----------|-------|-------------|
| sihot_url | https://sihot.example.com/services | URL base de Sihot |
| sihot_user | usuario_api | Usuario de API |
| sihot_password | contraseña_segura | Contraseña de API |
| sihot_hotel_id | 123 | ID del hotel en Sihot |
| security_id | token_obtenido | Token de seguridad (se actualiza automáticamente) |
| valid_until | fecha_expiracion | Fecha de expiración del token |

## Manejo de Errores

Todas las operaciones de Sihot devuelven un objeto Result con los siguientes campos:

- **Success** - "true" si la operación fue exitosa, "false" en caso contrario
- **ErrorMsg** - Mensaje de error en caso de fallo
- **MSG-LIST** - Lista de mensajes adicionales (puede contener detalles sobre el error)

Es importante verificar siempre el valor de Success antes de procesar los resultados.

## Consideraciones de Seguridad

- Almacena las credenciales de forma segura
- Utiliza HTTPS para todas las comunicaciones
- Renueva los tokens de autenticación antes de que expiren
- No expongas los endpoints de Sihot directamente a Internet
- Implementa un sistema de registro para auditar todas las operaciones

## Soporte y Recursos Adicionales

Si encuentras problemas o necesitas más información:

1. Consulta la documentación oficial de Sihot
2. Contacta con el soporte técnico de Sihot
3. Revisa los logs de tu aplicación para identificar errores
4. Utiliza herramientas como SoapUI o Postman para depurar las solicitudes

## Próximos Pasos

1. Revisa la documentación de cada operación específica
2. Configura tu entorno de desarrollo
3. Implementa las operaciones necesarias para tu caso de uso
4. Prueba exhaustivamente cada operación antes de pasar a producción
