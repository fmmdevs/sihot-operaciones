# Autenticación en Sihot

## Operación: S_AUTHENTICATE_V003

### Descripción
Esta operación se utiliza para autenticarse en el sistema Sihot y obtener un token de seguridad (SecurityID) que será necesario para realizar otras operaciones, como la confirmación de notificaciones.

### URL del Endpoint
```
POST https://[sihot-instance-url]/services/PushNotificationsGlobal
```

### Método HTTP
POST

### Encabezados (Headers)
```
Content-Type: text/xml; charset=utf-8
SOAPAction: "http://www.sihot.com/schemas/PushNotificationsGlobal.xsd/S_AUTHENTICATE_V003"
```

### Estructura de la Solicitud XML

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:push="http://www.sihot.com/schemas/PushNotificationsGlobal.xsd">
   <soapenv:Header/>
   <soapenv:Body>
      <push:S_AUTHENTICATE_V003>
         <AuthenticationInfos>
            <user>nombre_usuario</user>
            <password>contraseña</password>
            <hotel>1</hotel>
            <product>ID_PRODUCTO</product>
         </AuthenticationInfos>
      </push:S_AUTHENTICATE_V003>
   </soapenv:Body>
</soapenv:Envelope>
```

#### Parámetros de la Solicitud

| Parámetro | Tipo | Requerido | Descripción |
|-----------|------|-----------|-------------|
| user | string | Sí | Nombre de usuario para la autenticación |
| password | string | Sí | Contraseña del usuario |
| hotel | int | Sí | ID del hotel en Sihot |
| product | string | No | ID del producto (opcional, depende de la configuración) |

### Ejemplo de Solicitud XML Completa

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:push="http://www.sihot.com/schemas/PushNotificationsGlobal.xsd">
   <soapenv:Header/>
   <soapenv:Body>
      <push:S_AUTHENTICATE_V003>
         <AuthenticationInfos>
            <user>api_user</user>
            <password>api_password</password>
            <hotel>123</hotel>
            <product>BINDING_ID</product>
         </AuthenticationInfos>
      </push:S_AUTHENTICATE_V003>
   </soapenv:Body>
</soapenv:Envelope>
```

### Estructura de la Respuesta XML

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Body>
      <S_AUTHENTICATE_V003Response xmlns="http://www.sihot.com/schemas/PushNotificationsGlobal.xsd">
         <Result>
            <Success>true</Success>
            <ErrorMsg></ErrorMsg>
            <MSG-LIST></MSG-LIST>
         </Result>
         <Authentication>
            <SecurityID>token-de-seguridad</SecurityID>
            <ValidUntilUTC>2023-06-01T12:00:00Z</ValidUntilUTC>
         </Authentication>
      </S_AUTHENTICATE_V003Response>
   </soap:Body>
</soap:Envelope>
```

#### Parámetros de la Respuesta

| Parámetro | Tipo | Descripción |
|-----------|------|-------------|
| Success | string | "true" si la autenticación fue exitosa, "false" en caso contrario |
| ErrorMsg | string | Mensaje de error en caso de fallo |
| MSG-LIST | complex | Lista de mensajes adicionales (generalmente vacía en caso de éxito) |
| SecurityID | string | Token de seguridad para usar en operaciones posteriores |
| ValidUntilUTC | datetime | Fecha y hora de expiración del token de seguridad en UTC |

### Flujo de Procesamiento

1. Tu sistema envía una solicitud de autenticación con las credenciales configuradas
2. Sihot valida las credenciales y genera un token de seguridad
3. Sihot responde con el token y su fecha de expiración
4. Tu sistema almacena el token y lo utiliza para operaciones posteriores
5. Tu sistema debe renovar el token antes de que expire

### Ejemplo de Implementación en Postman

Para probar esta operación en Postman, sigue estos pasos:

1. Crea una nueva solicitud POST
2. Establece la URL: `https://[sihot-instance-url]/services/PushNotificationsGlobal`
3. Configura los encabezados:
   ```
   Content-Type: text/xml; charset=utf-8
   SOAPAction: "http://www.sihot.com/schemas/PushNotificationsGlobal.xsd/S_AUTHENTICATE_V003"
   ```
4. En el cuerpo de la solicitud, selecciona "raw" y copia el ejemplo XML anterior
5. Reemplaza los valores de ejemplo con los datos reales de tu entorno
6. Envía la solicitud

### Código de Ejemplo en TypeScript

```typescript
async function authenticate(
  sihotConfig: SihotConfig,
): Promise<[string, DateTime]> {
  const soapClient = await getClient(sihotConfig);

  const S_AUTHENTICATE_V003Request = {
    AuthenticationInfos: {
      user: sihotConfig.user,
      password: sihotConfig.password,
      hotel: parseInt(sihotConfig.sihot_hotel_id),
      product: sihotConfig.use_productid ? sihotProductId : undefined,
    },
  };

  let res;

  try {
    [res] = await soapClient.S_AUTHENTICATE_V003Async(
      S_AUTHENTICATE_V003Request,
    );
  } catch (e) {
    let error = '' + e;
    if (typeof e === 'object' && e && 'Fault' in e) {
      error = JSON.stringify(e.Fault);
    }

    throw new Error('Error de conexión: ' + error);
  }

  if (res.Result.Success !== 'true') {
    throw new Error(
      'Autenticación fallida: ' + JSON.stringify(res.Result['MSG-LIST']),
    );
  } else if (!res.Authentication) {
    throw new Error('Respuesta malformada');
  }

  const securityId = res.Authentication.SecurityID;
  const expiration = DateTime.fromISO(res.Authentication.ValidUntilUTC, {
    zone: 'utc',
  })
    .minus({ seconds: 5 })
    .setZone('local');

  return [securityId, expiration];
}
```

### Gestión de Tokens

Es recomendable implementar un sistema de caché para los tokens de autenticación:

```typescript
private readonly connections = new Map<string, SihotConnection>();

interface SihotConnection {
  mutex: Mutex;
  expiration: DateTime;
  securityId?: string;
}

async function getAuthentication(
  sihotConfig: SihotConfig,
): Promise<SihotAuthentication> {
  const configId = sihotConfig.id;
  
  // Verificar si ya existe una conexión
  if (!this.connections.has(configId)) {
    this.connections.set(configId, {
      mutex: new Mutex(),
      expiration: DateTime.fromMillis(0),
    });
  }
  
  const connection = this.connections.get(configId)!;
  
  // Adquirir mutex para evitar múltiples autenticaciones simultáneas
  return connection.mutex.runExclusive(async () => {
    const now = DateTime.now();
    
    // Verificar si el token ha expirado
    if (!connection.securityId || now >= connection.expiration) {
      // Obtener nuevo token
      const [securityId, expiration] = await authenticate(sihotConfig);
      connection.securityId = securityId;
      connection.expiration = expiration;
    }
    
    return {
      SecurityID: connection.securityId,
      ValidUntilUTC: connection.expiration.toUTC().toISO(),
    };
  });
}
```

### Notas Importantes

- Los tokens de autenticación tienen una validez limitada (generalmente 1 hora)
- Es recomendable renovar el token unos segundos antes de que expire
- Utiliza un sistema de mutex para evitar múltiples autenticaciones simultáneas
- Almacena los tokens en memoria para reutilizarlos en múltiples operaciones
- Maneja adecuadamente los errores de autenticación
