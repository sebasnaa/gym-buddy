# GymBuddy — API Specification

Version: 0.1
API style: REST
Payload format: JSON
Authentication: Secure session cookie or equivalent server-managed session

---

# Objetivo

Definir el contrato HTTP del MVP de GymBuddy.

Este documento es la fuente de verdad para:

* Endpoints.
* Métodos HTTP.
* Autenticación.
* Autorización.
* Requests.
* Responses.
* Validaciones.
* Códigos de estado.
* Errores.
* Paginación.
* Exposición de datos sensibles.

Lovable debe revisar este documento antes de crear o modificar rutas de la API.

---

# Principios generales

La API debe cumplir estas reglas:

* Usar JSON para requests y responses.
* Mantener contratos estables y consistentes.
* Validar todos los datos en el backend.
* No confiar en identificadores de usuario enviados por el cliente.
* Obtener el usuario autenticado desde la sesión.
* No exponer contraseñas, hashes, tokens ni datos de contacto no autorizados.
* Aplicar autorización en cada endpoint privado.
* Evitar lógica de negocio en controladores.
* Versionar la API desde el inicio.
* Usar códigos HTTP correctos.
* No devolver errores internos o trazas al cliente.
* Registrar errores técnicos sin incluir datos sensibles.
* Limitar el tamaño de los payloads.
* Aplicar rate limiting donde corresponda.

---

# URL base

```text
/api/v1
```

Todos los endpoints definidos en este documento parten de esa ruta.

Ejemplo:

```text
POST /api/v1/auth/register
```

---

# Formato de respuesta

## Respuesta correcta con datos

```json
{
  "success": true,
  "data": {
    "id": "uuid"
  }
}
```

## Respuesta correcta sin contenido de negocio

```json
{
  "success": true,
  "data": null
}
```

## Respuesta con paginación

```json
{
  "success": true,
  "data": [
    {}
  ],
  "pagination": {
    "page": 1,
    "pageSize": 20,
    "totalItems": 84,
    "totalPages": 5,
    "hasNextPage": true,
    "hasPreviousPage": false
  }
}
```

## Respuesta de error

```json
{
  "success": false,
  "error": {
    "code": "PROFILE_NOT_FOUND",
    "message": "No se ha encontrado el perfil.",
    "details": null
  }
}
```

## Error de validación

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Los datos enviados no son válidos.",
    "details": {
      "email": [
        "El correo electrónico no es válido."
      ],
      "password": [
        "La contraseña debe tener al menos 10 caracteres."
      ]
    }
  }
}
```

---

# Identificador de correlación

Cada respuesta debería incluir una cabecera:

```text
X-Request-Id
```

Este identificador permite relacionar errores del cliente con logs del servidor.

No debe contener información del usuario.

---

# Códigos HTTP

## 200 OK

Petición procesada correctamente.

## 201 Created

Recurso creado correctamente.

## 204 No Content

Operación correcta sin cuerpo de respuesta.

En el MVP se permite usar `200` con el formato común de respuesta para mantener consistencia.

## 400 Bad Request

Request mal formado o regla de negocio inválida.

## 401 Unauthorized

No existe una sesión válida.

## 403 Forbidden

El usuario está autenticado, pero no tiene permiso.

## 404 Not Found

El recurso no existe o no debe revelarse su existencia.

## 409 Conflict

La operación entra en conflicto con el estado actual.

Ejemplos:

* Correo ya registrado.
* Solicitud duplicada.
* Match ya existente.

## 422 Unprocessable Entity

Los campos tienen formato válido, pero no superan validaciones semánticas.

Para simplificar el MVP, puede utilizarse `400` para validaciones, siempre que se mantenga la convención en todos los endpoints.

## 429 Too Many Requests

Se ha superado el límite de peticiones.

## 500 Internal Server Error

Error no controlado.

Nunca devolver detalles internos.

---

# Autenticación

La autenticación debe basarse en una sesión segura gestionada por el servidor.

Preferencia:

```text
Cookie httpOnly + secure + sameSite
```

La sesión debe identificar al usuario autenticado.

El frontend no debe enviar manualmente:

```text
userId
role
accountStatus
```

como fuente de autorización.

## Cookie de sesión

Requisitos:

* `httpOnly`
* `secure` en producción
* `sameSite=lax` o política más estricta cuando sea posible
* Expiración controlada
* Rotación tras iniciar sesión
* Invalidación al cerrar sesión
* Invalidación tras cambio de contraseña
* Invalidación cuando la cuenta se suspende o elimina

---

# Protección CSRF

Cuando la autenticación se base en cookies, los endpoints mutables deben protegerse contra CSRF.

Afecta especialmente a:

```text
POST
PATCH
PUT
DELETE
```

Se puede utilizar:

* Token CSRF.
* SameSite y validación de origen.
* Patrón compatible con la librería de autenticación elegida.

---

# Roles

## USER

Puede:

* Gestionar su propia cuenta.
* Gestionar su perfil.
* Gestionar sus gimnasios.
* Gestionar su disponibilidad.
* Buscar candidatos.
* Enviar solicitudes.
* Responder solicitudes recibidas.
* Consultar sus matches.
* Ver contactos autorizados.
* Enviar feedback.
* Bloquear usuarios.
* Reportar usuarios.

## ADMIN

Puede realizar las acciones de usuario y además:

* Consultar métricas agregadas.
* Gestionar gimnasios.
* Consultar reportes.
* Resolver reportes.
* Suspender o reactivar cuentas.

---

# Endpoints de salud

## GET `/health`

Comprueba que la aplicación está activa.

### Autenticación

No requerida.

### Response `200`

```json
{
  "success": true,
  "data": {
    "status": "ok",
    "timestamp": "2026-07-31T10:00:00.000Z"
  }
}
```

No debe exponer:

* Versión exacta de dependencias.
* Variables de entorno.
* Credenciales.
* Detalles internos de infraestructura.

---

## GET `/health/ready`

Comprueba que la aplicación y PostgreSQL están disponibles.

### Autenticación

Puede limitarse a infraestructura interna en producción.

### Response `200`

```json
{
  "success": true,
  "data": {
    "status": "ready"
  }
}
```

### Response `503`

```json
{
  "success": false,
  "error": {
    "code": "SERVICE_NOT_READY",
    "message": "El servicio no está disponible temporalmente.",
    "details": null
  }
}
```

---

# Autenticación

## POST `/auth/register`

Crea una cuenta de usuario.

### Autenticación

No requerida.

### Request

```json
{
  "email": "maria@example.com",
  "password": "UnaContrasenaSegura123!",
  "acceptTerms": true,
  "acceptPrivacyPolicy": true
}
```

### Validaciones

* `email` obligatorio.
* Normalizar `email` a minúsculas.
* Formato válido.
* Longitud máxima razonable.
* El correo no debe estar registrado.
* `password` obligatoria.
* Contraseña mínima de 10 caracteres.
* Requerir variedad suficiente o aplicar un medidor robusto.
* Comprobar contraseñas demasiado comunes.
* `acceptTerms` debe ser `true`.
* `acceptPrivacyPolicy` debe ser `true`.

### Response `201`

```json
{
  "success": true,
  "data": {
    "user": {
      "id": "2d0c10b2-b989-48ac-885e-2fa4020ba83a",
      "email": "maria@example.com",
      "role": "USER",
      "status": "ACTIVE",
      "profileCompleted": false,
      "createdAt": "2026-07-31T10:00:00.000Z"
    }
  }
}
```

La implementación puede iniciar sesión automáticamente tras el registro.

### Errores

```text
EMAIL_ALREADY_REGISTERED
INVALID_EMAIL
WEAK_PASSWORD
TERMS_NOT_ACCEPTED
PRIVACY_POLICY_NOT_ACCEPTED
RATE_LIMIT_EXCEEDED
```

### Seguridad

Aplicar rate limiting por IP y por correo normalizado.

No indicar diferencias innecesarias que faciliten enumeración masiva de cuentas.

---

## POST `/auth/login`

Inicia sesión.

### Autenticación

No requerida.

### Request

```json
{
  "email": "maria@example.com",
  "password": "UnaContrasenaSegura123!"
}
```

### Validaciones

* Correo y contraseña obligatorios.
* Normalizar correo.
* Cuenta activa.
* Comparación segura del hash.
* Protección contra fuerza bruta.

### Response `200`

```json
{
  "success": true,
  "data": {
    "user": {
      "id": "2d0c10b2-b989-48ac-885e-2fa4020ba83a",
      "email": "maria@example.com",
      "role": "USER",
      "status": "ACTIVE",
      "profileCompleted": true
    }
  }
}
```

### Errores

```text
INVALID_CREDENTIALS
ACCOUNT_SUSPENDED
ACCOUNT_DELETED
RATE_LIMIT_EXCEEDED
```

No distinguir públicamente entre correo inexistente y contraseña incorrecta.

Usar el mismo error:

```text
INVALID_CREDENTIALS
```

---

## POST `/auth/logout`

Cierra la sesión actual.

### Autenticación

Requerida.

### Request

Sin cuerpo.

### Response `200`

```json
{
  "success": true,
  "data": null
}
```

La sesión debe invalidarse en el servidor.

---

## GET `/auth/session`

Devuelve información mínima de la sesión actual.

### Autenticación

Requerida.

### Response `200`

```json
{
  "success": true,
  "data": {
    "user": {
      "id": "2d0c10b2-b989-48ac-885e-2fa4020ba83a",
      "email": "maria@example.com",
      "role": "USER",
      "status": "ACTIVE",
      "profileCompleted": true
    }
  }
}
```

### Response `401`

```json
{
  "success": false,
  "error": {
    "code": "UNAUTHENTICATED",
    "message": "Debes iniciar sesión.",
    "details": null
  }
}
```

---

## POST `/auth/forgot-password`

Solicita restablecimiento de contraseña.

Puede quedar preparado aunque el envío de correo se posponga.

### Autenticación

No requerida.

### Request

```json
{
  "email": "maria@example.com"
}
```

### Response `200`

La respuesta debe ser idéntica exista o no exista la cuenta.

```json
{
  "success": true,
  "data": {
    "message": "Si existe una cuenta asociada, recibirás instrucciones."
  }
}
```

### Seguridad

* Token de un solo uso.
* Expiración corta.
* Almacenar solo hash del token.
* Invalidar tokens anteriores.
* Rate limiting estricto.

---

## POST `/auth/reset-password`

Establece una nueva contraseña.

### Autenticación

No requerida.

### Request

```json
{
  "token": "opaque-reset-token",
  "newPassword": "OtraContrasenaSegura123!"
}
```

### Response `200`

```json
{
  "success": true,
  "data": null
}
```

### Errores

```text
INVALID_RESET_TOKEN
EXPIRED_RESET_TOKEN
WEAK_PASSWORD
```

Al completar el cambio:

* Invalidar el token.
* Invalidar sesiones existentes.
* Registrar el evento de seguridad.

---

# Cuenta

## GET `/account`

Devuelve la información privada de la cuenta autenticada.

### Autenticación

Requerida.

### Response `200`

```json
{
  "success": true,
  "data": {
    "id": "2d0c10b2-b989-48ac-885e-2fa4020ba83a",
    "email": "maria@example.com",
    "role": "USER",
    "status": "ACTIVE",
    "emailVerifiedAt": null,
    "lastLoginAt": "2026-07-31T09:30:00.000Z",
    "createdAt": "2026-07-20T14:00:00.000Z"
  }
}
```

---

## PATCH `/account/email`

Cambia el correo de la cuenta.

### Autenticación

Requerida.

### Request

```json
{
  "newEmail": "nuevo-correo@example.com",
  "currentPassword": "UnaContrasenaSegura123!"
}
```

### Response `200`

```json
{
  "success": true,
  "data": {
    "email": "nuevo-correo@example.com",
    "emailVerifiedAt": null
  }
}
```

### Errores

```text
INVALID_CREDENTIALS
EMAIL_ALREADY_REGISTERED
INVALID_EMAIL
```

---

## PATCH `/account/password`

Cambia la contraseña.

### Autenticación

Requerida.

### Request

```json
{
  "currentPassword": "UnaContrasenaSegura123!",
  "newPassword": "OtraContrasenaSegura456!"
}
```

### Response `200`

```json
{
  "success": true,
  "data": null
}
```

### Comportamiento

* Verificar contraseña actual.
* Validar nueva contraseña.
* Actualizar hash.
* Invalidar otras sesiones.
* Mantener o renovar la sesión actual según la estrategia elegida.

---

## DELETE `/account`

Solicita el borrado lógico de la cuenta.

### Autenticación

Requerida.

### Request

```json
{
  "currentPassword": "UnaContrasenaSegura123!",
  "confirmation": "ELIMINAR"
}
```

### Response `200`

```json
{
  "success": true,
  "data": null
}
```

### Comportamiento

* Establecer estado `DELETED`.
* Añadir `deletedAt`.
* Desactivar visibilidad.
* Cancelar solicitudes pendientes.
* Archivar matches cuando corresponda.
* Invalidar sesiones.
* Ocultar datos de contacto.
* Aplicar política de anonimización.

---

# Perfil

## GET `/profile/me`

Devuelve el perfil completo del usuario autenticado.

### Autenticación

Requerida.

### Response `200`

```json
{
  "success": true,
  "data": {
    "id": "c8e680b4-2903-47ef-8668-d334a05178be",
    "userId": "2d0c10b2-b989-48ac-885e-2fa4020ba83a",
    "displayName": "María",
    "birthDate": "1995-06-12",
    "age": 31,
    "gender": "WOMAN",
    "city": "Huelva",
    "bio": "Entreno fuerza cuatro días por semana.",
    "photoUrl": null,
    "experienceLevel": "INTERMEDIATE",
    "primaryGoal": "STRENGTH",
    "buddyReason": "NEED_MOTIVATION",
    "trainingPreference": "SOMETIMES_WITH_PARTNER",
    "contactType": "TELEGRAM",
    "contactValue": "@maria_entrena",
    "profileCompleted": true,
    "visibilityEnabled": true,
    "createdAt": "2026-07-20T14:10:00.000Z",
    "updatedAt": "2026-07-30T18:10:00.000Z"
  }
}
```

El campo `age` puede calcularse en el servidor y no debe persistirse.

---

## POST `/profile`

Crea el perfil del usuario autenticado.

### Autenticación

Requerida.

### Request

```json
{
  "displayName": "María",
  "birthDate": "1995-06-12",
  "gender": "WOMAN",
  "city": "Huelva",
  "bio": "Entreno fuerza cuatro días por semana.",
  "experienceLevel": "INTERMEDIATE",
  "primaryGoal": "STRENGTH",
  "buddyReason": "NEED_MOTIVATION",
  "trainingPreference": "SOMETIMES_WITH_PARTNER",
  "contactType": "TELEGRAM",
  "contactValue": "@maria_entrena"
}
```

### Response `201`

```json
{
  "success": true,
  "data": {
    "id": "c8e680b4-2903-47ef-8668-d334a05178be",
    "profileCompleted": false
  }
}
```

El perfil puede seguir incompleto hasta que existan gimnasio y disponibilidad.

### Errores

```text
PROFILE_ALREADY_EXISTS
UNDERAGE_USER
INVALID_CONTACT_VALUE
INVALID_ENUM_VALUE
```

---

## PATCH `/profile/me`

Actualiza el perfil.

### Autenticación

Requerida.

### Request parcial

```json
{
  "bio": "Busco compañero para fuerza y sentadilla.",
  "primaryGoal": "POWERLIFTING",
  "trainingPreference": "ONLY_FOR_SPECIFIC_WORKOUTS"
}
```

### Response `200`

```json
{
  "success": true,
  "data": {
    "id": "c8e680b4-2903-47ef-8668-d334a05178be",
    "profileCompleted": true,
    "updatedAt": "2026-07-31T10:30:00.000Z"
  }
}
```

### Restricciones

No permitir actualizar mediante este endpoint:

```text
userId
role
status
createdAt
```

---

## PATCH `/profile/me/visibility`

Activa o desactiva la aparición en resultados.

### Autenticación

Requerida.

### Request

```json
{
  "visibilityEnabled": false
}
```

### Response `200`

```json
{
  "success": true,
  "data": {
    "visibilityEnabled": false
  }
}
```

Desactivar la visibilidad no elimina solicitudes o matches existentes.

---

## GET `/profiles/:profileId`

Devuelve la versión pública de un perfil.

### Autenticación

Requerida.

### Response `200`

```json
{
  "success": true,
  "data": {
    "id": "81b897e7-c327-4416-b4ca-586ed1ccf3ae",
    "displayName": "Carlos",
    "age": 34,
    "city": "Huelva",
    "bio": "Busco spotter para press banca.",
    "photoUrl": null,
    "experienceLevel": "ADVANCED",
    "primaryGoal": "POWERLIFTING",
    "buddyReason": "NEED_SPOTTER",
    "trainingPreference": "ONLY_FOR_SPECIFIC_WORKOUTS",
    "gyms": [
      {
        "id": "d3921e30-73aa-4f98-bab6-93d203084817",
        "name": "Gym Centro",
        "city": "Huelva"
      }
    ]
  }
}
```

### Datos excluidos

Nunca devolver:

```text
email
birthDate
contactType
contactValue
passwordHash
accountStatus
```

### Errores

```text
PROFILE_NOT_FOUND
PROFILE_NOT_VISIBLE
USER_BLOCKED
```

Cuando sea adecuado, devolver `404` para evitar revelar la existencia del perfil.

---

# Foto de perfil

## POST `/profile/me/photo`

Sube o asigna una foto.

### Autenticación

Requerida.

### Content-Type

```text
multipart/form-data
```

### Restricciones

* Solo imágenes permitidas.
* Validar MIME real.
* Tamaño máximo configurable.
* Renombrar el archivo.
* No confiar en el nombre original.
* Eliminar metadatos EXIF cuando sea posible.
* Generar variantes optimizadas.
* No servir archivos ejecutables.

### Response `200`

```json
{
  "success": true,
  "data": {
    "photoUrl": "/uploads/profiles/generated-id.webp"
  }
}
```

Para simplificar el primer MVP, esta funcionalidad puede posponerse y utilizarse un avatar generado.

---

## DELETE `/profile/me/photo`

Elimina la foto del perfil.

### Autenticación

Requerida.

### Response `200`

```json
{
  "success": true,
  "data": {
    "photoUrl": null
  }
}
```

---

# Gimnasios

## GET `/gyms`

Busca gimnasios.

### Autenticación

Requerida.

### Query parameters

```text
query
city
verificationStatus
page
pageSize
```

### Ejemplo

```text
GET /gyms?query=centro&city=Huelva&page=1&pageSize=20
```

### Response `200`

```json
{
  "success": true,
  "data": [
    {
      "id": "d3921e30-73aa-4f98-bab6-93d203084817",
      "name": "Gym Centro",
      "city": "Huelva",
      "address": "Calle Ejemplo 10",
      "postalCode": "21001",
      "verificationStatus": "VERIFIED"
    }
  ],
  "pagination": {
    "page": 1,
    "pageSize": 20,
    "totalItems": 1,
    "totalPages": 1,
    "hasNextPage": false,
    "hasPreviousPage": false
  }
}
```

### Restricciones

* `pageSize` máxima: 50.
* Escapar y normalizar búsquedas.
* No interpolar texto directamente en SQL.

---

## POST `/gyms`

Propone un gimnasio nuevo.

### Autenticación

Requerida.

### Request

```json
{
  "name": "Nuevo Gym Huelva",
  "city": "Huelva",
  "address": "Avenida Ejemplo 5",
  "postalCode": "21002"
}
```

### Response `201`

```json
{
  "success": true,
  "data": {
    "id": "ea3cd913-2cf7-44b5-9532-a253f185fabc",
    "name": "Nuevo Gym Huelva",
    "city": "Huelva",
    "verificationStatus": "UNVERIFIED"
  }
}
```

### Errores

```text
GYM_ALREADY_EXISTS
INVALID_GYM_NAME
INVALID_CITY
```

Antes de crear, buscar duplicados por nombre normalizado y ciudad.

---

## GET `/gyms/:gymId`

Devuelve un gimnasio.

### Autenticación

Requerida.

### Response `200`

```json
{
  "success": true,
  "data": {
    "id": "d3921e30-73aa-4f98-bab6-93d203084817",
    "name": "Gym Centro",
    "city": "Huelva",
    "address": "Calle Ejemplo 10",
    "postalCode": "21001",
    "verificationStatus": "VERIFIED"
  }
}
```

---

# Gimnasios del usuario

## GET `/profile/me/gyms`

Devuelve los gimnasios asociados al usuario.

### Autenticación

Requerida.

### Response `200`

```json
{
  "success": true,
  "data": [
    {
      "id": "0ed16f80-9972-442a-8aa1-61534b82fef8",
      "gym": {
        "id": "d3921e30-73aa-4f98-bab6-93d203084817",
        "name": "Gym Centro",
        "city": "Huelva"
      },
      "isPrimary": true,
      "joinedAt": "2025-09-01"
    }
  ]
}
```

---

## POST `/profile/me/gyms`

Asocia un gimnasio al usuario.

### Autenticación

Requerida.

### Request

```json
{
  "gymId": "d3921e30-73aa-4f98-bab6-93d203084817",
  "isPrimary": true,
  "joinedAt": "2025-09-01"
}
```

### Response `201`

```json
{
  "success": true,
  "data": {
    "id": "0ed16f80-9972-442a-8aa1-61534b82fef8",
    "gymId": "d3921e30-73aa-4f98-bab6-93d203084817",
    "isPrimary": true
  }
}
```

### Comportamiento

Si `isPrimary = true`, cualquier otro gimnasio principal debe dejar de serlo dentro de la misma transacción.

### Errores

```text
GYM_NOT_FOUND
USER_GYM_ALREADY_EXISTS
```

---

## PATCH `/profile/me/gyms/:userGymId`

Modifica la relación.

### Autenticación

Requerida.

### Request

```json
{
  "isPrimary": true,
  "joinedAt": "2025-08-15"
}
```

### Response `200`

```json
{
  "success": true,
  "data": {
    "id": "0ed16f80-9972-442a-8aa1-61534b82fef8",
    "isPrimary": true,
    "joinedAt": "2025-08-15"
  }
}
```

El usuario solo puede modificar relaciones propias.

---

## DELETE `/profile/me/gyms/:userGymId`

Elimina la asociación con un gimnasio.

### Autenticación

Requerida.

### Response `200`

```json
{
  "success": true,
  "data": null
}
```

### Restricciones

* No permitir dejar el perfil completo sin ningún gimnasio sin recalcular `profileCompleted`.
* Si se elimina el gimnasio principal, otro puede convertirse en principal.
* No modificar matches históricos.

---

# Disponibilidad

## GET `/profile/me/availability`

Devuelve las franjas horarias del usuario.

### Autenticación

Requerida.

### Response `200`

```json
{
  "success": true,
  "data": [
    {
      "id": "c467f601-b870-42bb-ac83-784a8d1a9794",
      "dayOfWeek": 1,
      "startTime": "18:00",
      "endTime": "20:00",
      "timezone": "Europe/Madrid"
    }
  ]
}
```

---

## POST `/profile/me/availability`

Crea una franja.

### Autenticación

Requerida.

### Request

```json
{
  "dayOfWeek": 1,
  "startTime": "18:00",
  "endTime": "20:00",
  "timezone": "Europe/Madrid"
}
```

### Response `201`

```json
{
  "success": true,
  "data": {
    "id": "c467f601-b870-42bb-ac83-784a8d1a9794",
    "dayOfWeek": 1,
    "startTime": "18:00",
    "endTime": "20:00",
    "timezone": "Europe/Madrid"
  }
}
```

### Validaciones

* Día entre 1 y 7.
* Inicio anterior a fin.
* Zona horaria permitida.
* Evitar duplicados exactos.
* Definir política para solapamientos.

Para el MVP, se pueden permitir franjas solapadas y normalizarlas posteriormente.

---

## PATCH `/profile/me/availability/:availabilityId`

Actualiza una franja.

### Autenticación

Requerida.

### Request

```json
{
  "startTime": "18:30",
  "endTime": "21:00"
}
```

### Response `200`

```json
{
  "success": true,
  "data": {
    "id": "c467f601-b870-42bb-ac83-784a8d1a9794",
    "dayOfWeek": 1,
    "startTime": "18:30",
    "endTime": "21:00",
    "timezone": "Europe/Madrid"
  }
}
```

---

## DELETE `/profile/me/availability/:availabilityId`

Elimina una franja.

### Autenticación

Requerida.

### Response `200`

```json
{
  "success": true,
  "data": null
}
```

Recalcular si el perfil sigue completo.

---

# Marcas de fuerza

## GET `/profile/me/strength-marks`

Devuelve las marcas del usuario.

### Autenticación

Requerida.

### Response `200`

```json
{
  "success": true,
  "data": [
    {
      "id": "d721e164-b0f7-4a4b-998d-92a56469d174",
      "exercise": "BENCH_PRESS",
      "weightKg": 80,
      "repetitions": 5,
      "estimatedOneRepMax": 90,
      "recordedAt": "2026-07-15"
    }
  ]
}
```

---

## POST `/profile/me/strength-marks`

Crea una marca.

### Autenticación

Requerida.

### Request

```json
{
  "exercise": "BENCH_PRESS",
  "weightKg": 80,
  "repetitions": 5,
  "recordedAt": "2026-07-15"
}
```

### Response `201`

```json
{
  "success": true,
  "data": {
    "id": "d721e164-b0f7-4a4b-998d-92a56469d174",
    "exercise": "BENCH_PRESS",
    "weightKg": 80,
    "repetitions": 5,
    "estimatedOneRepMax": 90
  }
}
```

### Restricciones

* Peso mayor que cero.
* Repeticiones entre 1 y un máximo razonable.
* No usar estas marcas como requisito obligatorio del matching.

---

## DELETE `/profile/me/strength-marks/:strengthMarkId`

Elimina una marca.

### Autenticación

Requerida.

### Response `200`

```json
{
  "success": true,
  "data": null
}
```

---

# Matching

## GET `/matches/candidates`

Devuelve candidatos compatibles.

### Autenticación

Requerida.

### Requisitos previos

* Cuenta activa.
* Perfil completo.
* Visibilidad habilitada.
* Al menos un gimnasio.
* Al menos una disponibilidad.

### Query parameters

```text
gymId
dayOfWeek
experienceLevel
primaryGoal
minimumScore
page
pageSize
```

Todos los filtros son opcionales salvo que la lógica de producto requiera seleccionar gimnasio.

### Ejemplo

```text
GET /matches/candidates?gymId=d3921e30-73aa-4f98-bab6-93d203084817&minimumScore=60&page=1&pageSize=20
```

### Response `200`

```json
{
  "success": true,
  "data": [
    {
      "userId": "4d3d11ad-8cf3-433f-a81e-b222238c9624",
      "profileId": "81b897e7-c327-4416-b4ca-586ed1ccf3ae",
      "displayName": "Carlos",
      "age": 34,
      "city": "Huelva",
      "bio": "Busco spotter para press banca.",
      "photoUrl": null,
      "experienceLevel": "ADVANCED",
      "primaryGoal": "POWERLIFTING",
      "buddyReason": "NEED_SPOTTER",
      "trainingPreference": "ONLY_FOR_SPECIFIC_WORKOUTS",
      "gym": {
        "id": "d3921e30-73aa-4f98-bab6-93d203084817",
        "name": "Gym Centro"
      },
      "sharedAvailability": [
        {
          "dayOfWeek": 1,
          "overlapStart": "18:30",
          "overlapEnd": "20:00"
        }
      ],
      "compatibility": {
        "score": 82,
        "reasons": [
          "Mismo gimnasio",
          "Horario compatible",
          "Objetivo relacionado"
        ]
      },
      "requestState": "NONE"
    }
  ],
  "pagination": {
    "page": 1,
    "pageSize": 20,
    "totalItems": 5,
    "totalPages": 1,
    "hasNextPage": false,
    "hasPreviousPage": false
  }
}
```

### Datos excluidos

Nunca devolver:

```text
email
birthDate
contactType
contactValue
exactAddress
passwordHash
```

### Exclusiones obligatorias

No incluir:

* Usuario actual.
* Usuarios suspendidos o eliminados.
* Perfiles incompletos.
* Perfiles ocultos.
* Usuarios bloqueados en cualquier dirección.
* Matches activos.
* Solicitudes pendientes equivalentes.
* Rechazos dentro del periodo de exclusión.
* Usuarios sin gimnasio compartido.
* Usuarios sin disponibilidad compatible.

### Orden

Orden inicial recomendado:

1. Mayor puntuación.
2. Mayor solapamiento horario.
3. Actividad reciente.
4. Identificador estable como desempate.

---

## GET `/matches/candidates/:userId`

Devuelve el detalle público de un candidato con información de compatibilidad.

### Autenticación

Requerida.

### Response `200`

```json
{
  "success": true,
  "data": {
    "userId": "4d3d11ad-8cf3-433f-a81e-b222238c9624",
    "profile": {
      "displayName": "Carlos",
      "age": 34,
      "city": "Huelva",
      "bio": "Busco spotter para press banca.",
      "experienceLevel": "ADVANCED",
      "primaryGoal": "POWERLIFTING",
      "buddyReason": "NEED_SPOTTER",
      "trainingPreference": "ONLY_FOR_SPECIFIC_WORKOUTS"
    },
    "compatibility": {
      "score": 82,
      "reasons": [
        "Entrenáis en el mismo gimnasio",
        "Coincidís los lunes de 18:30 a 20:00"
      ]
    }
  }
}
```

Debe aplicar las mismas exclusiones de privacidad que el listado.

---

# Solicitudes de match

## POST `/match-requests`

Envía una solicitud.

### Autenticación

Requerida.

### Request

```json
{
  "receiverId": "4d3d11ad-8cf3-433f-a81e-b222238c9624",
  "gymId": "d3921e30-73aa-4f98-bab6-93d203084817",
  "message": "¿Te viene bien entrenar el lunes sobre las 19:00?"
}
```

### Response `201`

```json
{
  "success": true,
  "data": {
    "id": "f4631be4-3758-45b2-8fd0-e855a2948a36",
    "senderId": "2d0c10b2-b989-48ac-885e-2fa4020ba83a",
    "receiverId": "4d3d11ad-8cf3-433f-a81e-b222238c9624",
    "gymId": "d3921e30-73aa-4f98-bab6-93d203084817",
    "compatibilityScore": 82,
    "status": "PENDING",
    "message": "¿Te viene bien entrenar el lunes sobre las 19:00?",
    "createdAt": "2026-07-31T11:00:00.000Z",
    "expiresAt": "2026-08-14T11:00:00.000Z"
  }
}
```

### Validaciones

* Emisor distinto del receptor.
* Ambos usuarios activos.
* Perfiles completos y visibles.
* Gimnasio compartido.
* Disponibilidad compatible.
* No existe bloqueo.
* No existe match activo.
* No existe solicitud pendiente equivalente.
* Mensaje dentro de longitud permitida.
* Recalcular compatibilidad en el servidor.

El cliente nunca debe establecer directamente `compatibilityScore`.

### Errores

```text
CANNOT_REQUEST_SELF
RECEIVER_NOT_AVAILABLE
NO_SHARED_GYM
NO_SHARED_AVAILABILITY
MATCH_REQUEST_ALREADY_EXISTS
MATCH_ALREADY_EXISTS
USER_BLOCKED
RECENTLY_REJECTED
```

---

## GET `/match-requests/incoming`

Devuelve solicitudes recibidas.

### Autenticación

Requerida.

### Query parameters

```text
status
page
pageSize
```

### Response `200`

```json
{
  "success": true,
  "data": [
    {
      "id": "f4631be4-3758-45b2-8fd0-e855a2948a36",
      "sender": {
        "userId": "2d0c10b2-b989-48ac-885e-2fa4020ba83a",
        "profileId": "c8e680b4-2903-47ef-8668-d334a05178be",
        "displayName": "María",
        "photoUrl": null,
        "experienceLevel": "INTERMEDIATE",
        "primaryGoal": "STRENGTH"
      },
      "gym": {
        "id": "d3921e30-73aa-4f98-bab6-93d203084817",
        "name": "Gym Centro"
      },
      "compatibilityScore": 82,
      "message": "¿Te viene bien entrenar el lunes sobre las 19:00?",
      "status": "PENDING",
      "createdAt": "2026-07-31T11:00:00.000Z",
      "expiresAt": "2026-08-14T11:00:00.000Z"
    }
  ],
  "pagination": {
    "page": 1,
    "pageSize": 20,
    "totalItems": 1,
    "totalPages": 1,
    "hasNextPage": false,
    "hasPreviousPage": false
  }
}
```

No devolver el contacto del emisor.

---

## GET `/match-requests/outgoing`

Devuelve solicitudes enviadas.

### Autenticación

Requerida.

### Response `200`

La estructura es equivalente, sustituyendo `sender` por `receiver`.

---

## GET `/match-requests/:requestId`

Devuelve una solicitud concreta.

### Autenticación

Requerida.

### Autorización

Solo el emisor o el receptor pueden verla.

### Response `200`

```json
{
  "success": true,
  "data": {
    "id": "f4631be4-3758-45b2-8fd0-e855a2948a36",
    "sender": {
      "userId": "2d0c10b2-b989-48ac-885e-2fa4020ba83a",
      "displayName": "María"
    },
    "receiver": {
      "userId": "4d3d11ad-8cf3-433f-a81e-b222238c9624",
      "displayName": "Carlos"
    },
    "gym": {
      "id": "d3921e30-73aa-4f98-bab6-93d203084817",
      "name": "Gym Centro"
    },
    "compatibilityScore": 82,
    "status": "PENDING",
    "message": "¿Te viene bien entrenar el lunes sobre las 19:00?",
    "createdAt": "2026-07-31T11:00:00.000Z",
    "respondedAt": null,
    "expiresAt": "2026-08-14T11:00:00.000Z"
  }
}
```

---

## POST `/match-requests/:requestId/accept`

Acepta una solicitud recibida.

### Autenticación

Requerida.

### Autorización

Solo el receptor puede aceptar.

### Request

Sin cuerpo.

### Response `200`

```json
{
  "success": true,
  "data": {
    "request": {
      "id": "f4631be4-3758-45b2-8fd0-e855a2948a36",
      "status": "ACCEPTED",
      "respondedAt": "2026-07-31T11:30:00.000Z"
    },
    "match": {
      "id": "0986a728-65be-49a0-b7d2-f3e9f6aa4be2",
      "status": "ACTIVE",
      "gymId": "d3921e30-73aa-4f98-bab6-93d203084817",
      "contactRevealedAt": "2026-07-31T11:30:00.000Z"
    }
  }
}
```

### Comportamiento transaccional

Dentro de una única transacción:

1. Bloquear o comprobar la solicitud.
2. Verificar que sigue pendiente.
3. Verificar que no ha expirado.
4. Verificar que no existe bloqueo.
5. Cambiar estado a `ACCEPTED`.
6. Crear el match.
7. Establecer `contactRevealedAt`.
8. Registrar evento analítico.

### Errores

```text
MATCH_REQUEST_NOT_FOUND
NOT_REQUEST_RECEIVER
MATCH_REQUEST_NOT_PENDING
MATCH_REQUEST_EXPIRED
MATCH_ALREADY_EXISTS
USER_BLOCKED
```

---

## POST `/match-requests/:requestId/reject`

Rechaza una solicitud.

### Autenticación

Requerida.

### Autorización

Solo el receptor.

### Response `200`

```json
{
  "success": true,
  "data": {
    "id": "f4631be4-3758-45b2-8fd0-e855a2948a36",
    "status": "REJECTED",
    "respondedAt": "2026-07-31T11:30:00.000Z"
  }
}
```

No es obligatorio solicitar una razón.

---

## POST `/match-requests/:requestId/cancel`

Cancela una solicitud enviada.

### Autenticación

Requerida.

### Autorización

Solo el emisor.

### Response `200`

```json
{
  "success": true,
  "data": {
    "id": "f4631be4-3758-45b2-8fd0-e855a2948a36",
    "status": "CANCELLED"
  }
}
```

Solo se puede cancelar una solicitud pendiente.

---

# Matches activos

## GET `/matches`

Devuelve los matches del usuario.

### Autenticación

Requerida.

### Query parameters

```text
status
page
pageSize
```

### Response `200`

```json
{
  "success": true,
  "data": [
    {
      "id": "0986a728-65be-49a0-b7d2-f3e9f6aa4be2",
      "status": "ACTIVE",
      "otherUser": {
        "userId": "4d3d11ad-8cf3-433f-a81e-b222238c9624",
        "profileId": "81b897e7-c327-4416-b4ca-586ed1ccf3ae",
        "displayName": "Carlos",
        "photoUrl": null,
        "experienceLevel": "ADVANCED",
        "primaryGoal": "POWERLIFTING"
      },
      "gym": {
        "id": "d3921e30-73aa-4f98-bab6-93d203084817",
        "name": "Gym Centro"
      },
      "plannedWorkoutAt": null,
      "createdAt": "2026-07-31T11:30:00.000Z"
    }
  ],
  "pagination": {
    "page": 1,
    "pageSize": 20,
    "totalItems": 1,
    "totalPages": 1,
    "hasNextPage": false,
    "hasPreviousPage": false
  }
}
```

---

## GET `/matches/:matchId`

Devuelve el detalle de un match.

### Autenticación

Requerida.

### Autorización

El usuario debe pertenecer al match.

### Response `200`

```json
{
  "success": true,
  "data": {
    "id": "0986a728-65be-49a0-b7d2-f3e9f6aa4be2",
    "status": "ACTIVE",
    "otherUser": {
      "userId": "4d3d11ad-8cf3-433f-a81e-b222238c9624",
      "profileId": "81b897e7-c327-4416-b4ca-586ed1ccf3ae",
      "displayName": "Carlos",
      "photoUrl": null,
      "bio": "Busco spotter para press banca.",
      "experienceLevel": "ADVANCED",
      "primaryGoal": "POWERLIFTING"
    },
    "gym": {
      "id": "d3921e30-73aa-4f98-bab6-93d203084817",
      "name": "Gym Centro",
      "city": "Huelva"
    },
    "contact": {
      "type": "WHATSAPP",
      "value": "+34600000000"
    },
    "plannedWorkoutAt": null,
    "contactRevealedAt": "2026-07-31T11:30:00.000Z",
    "createdAt": "2026-07-31T11:30:00.000Z"
  }
}
```

### Reglas para mostrar contacto

Solo devolver `contact` cuando:

* El match está activo.
* El usuario autenticado pertenece al match.
* El otro usuario sigue activo.
* No existe bloqueo entre ambos.
* El contacto está configurado.

### Errores

```text
MATCH_NOT_FOUND
NOT_MATCH_PARTICIPANT
CONTACT_NOT_AVAILABLE
USER_BLOCKED
```

---

## PATCH `/matches/:matchId/workout`

Establece o modifica la fecha prevista del entrenamiento.

### Autenticación

Requerida.

### Request

```json
{
  "plannedWorkoutAt": "2026-08-03T17:00:00.000Z"
}
```

### Response `200`

```json
{
  "success": true,
  "data": {
    "id": "0986a728-65be-49a0-b7d2-f3e9f6aa4be2",
    "plannedWorkoutAt": "2026-08-03T17:00:00.000Z"
  }
}
```

### Restricciones

* El usuario debe pertenecer al match.
* El match debe estar activo.
* La fecha debe ser futura al asignarse.
* Guardar fechas en UTC.
* Presentarlas en la zona horaria del usuario.

Para el MVP, esta fecha puede ser compartida y modificable por ambos.

---

## POST `/matches/:matchId/archive`

Archiva un match.

### Autenticación

Requerida.

### Response `200`

```json
{
  "success": true,
  "data": {
    "id": "0986a728-65be-49a0-b7d2-f3e9f6aa4be2",
    "status": "ARCHIVED"
  }
}
```

Archivar no equivale a bloquear.

El contacto deja de mostrarse si esa es la política configurada.

---

# Feedback de entrenamiento

## GET `/matches/:matchId/feedback/me`

Devuelve el feedback del usuario para ese match.

### Autenticación

Requerida.

### Response `200`

```json
{
  "success": true,
  "data": {
    "id": "e98ec2ef-6f78-43d8-a2f5-bbe1c6676158",
    "workoutHappened": "YES",
    "wouldRepeat": "YES",
    "rating": 5,
    "comment": "Buen entrenamiento.",
    "submittedAt": "2026-08-03T20:00:00.000Z"
  }
}
```

### Response cuando no existe

Puede devolver:

```json
{
  "success": true,
  "data": null
}
```

---

## POST `/matches/:matchId/feedback`

Crea el feedback.

### Autenticación

Requerida.

### Request con entrenamiento realizado

```json
{
  "workoutHappened": "YES",
  "wouldRepeat": "YES",
  "rating": 5,
  "comment": "Buen entrenamiento y horarios compatibles."
}
```

### Request con entrenamiento no realizado

```json
{
  "workoutHappened": "NO",
  "wouldRepeat": null,
  "rating": null,
  "comment": "Finalmente no pudimos coincidir."
}
```

### Response `201`

```json
{
  "success": true,
  "data": {
    "id": "e98ec2ef-6f78-43d8-a2f5-bbe1c6676158",
    "matchId": "0986a728-65be-49a0-b7d2-f3e9f6aa4be2",
    "workoutHappened": "YES",
    "wouldRepeat": "YES",
    "rating": 5,
    "submittedAt": "2026-08-03T20:00:00.000Z"
  }
}
```

### Restricciones

* El usuario debe pertenecer al match.
* Solo un feedback por usuario y match.
* Rating entre 1 y 5.
* Comentario con longitud máxima.
* Si no hubo entrenamiento, `wouldRepeat` y `rating` pueden ser nulos.

### Errores

```text
FEEDBACK_ALREADY_EXISTS
NOT_MATCH_PARTICIPANT
INVALID_FEEDBACK
```

---

## PATCH `/matches/:matchId/feedback`

Actualiza el feedback propio.

### Autenticación

Requerida.

### Request

```json
{
  "wouldRepeat": "MAYBE",
  "rating": 4,
  "comment": "Buena experiencia, aunque nuestros ritmos son distintos."
}
```

### Response `200`

```json
{
  "success": true,
  "data": {
    "id": "e98ec2ef-6f78-43d8-a2f5-bbe1c6676158",
    "wouldRepeat": "MAYBE",
    "rating": 4
  }
}
```

---

# Bloqueos

## GET `/blocks`

Devuelve usuarios bloqueados por el usuario autenticado.

### Autenticación

Requerida.

### Response `200`

```json
{
  "success": true,
  "data": [
    {
      "id": "7e814aa7-df51-4452-87b2-43cb46d6d947",
      "blockedUser": {
        "userId": "4d3d11ad-8cf3-433f-a81e-b222238c9624",
        "displayName": "Carlos",
        "photoUrl": null
      },
      "createdAt": "2026-08-04T10:00:00.000Z"
    }
  ]
}
```

---

## POST `/blocks`

Bloquea a un usuario.

### Autenticación

Requerida.

### Request

```json
{
  "blockedUserId": "4d3d11ad-8cf3-433f-a81e-b222238c9624",
  "reason": "No deseo recibir más solicitudes de esta persona."
}
```

### Response `201`

```json
{
  "success": true,
  "data": {
    "id": "7e814aa7-df51-4452-87b2-43cb46d6d947",
    "blockedUserId": "4d3d11ad-8cf3-433f-a81e-b222238c9624",
    "createdAt": "2026-08-04T10:00:00.000Z"
  }
}
```

### Comportamiento transaccional

* Crear bloqueo.
* Cancelar solicitudes pendientes entre ambos.
* Ocultar ambos perfiles.
* Impedir nuevos matches.
* Archivar o marcar como bloqueados los matches activos.
* Dejar de revelar contactos.

### Errores

```text
CANNOT_BLOCK_SELF
USER_ALREADY_BLOCKED
USER_NOT_FOUND
```

---

## DELETE `/blocks/:blockedUserId`

Desbloquea a un usuario.

### Autenticación

Requerida.

### Response `200`

```json
{
  "success": true,
  "data": null
}
```

Desbloquear no restaura automáticamente:

* Solicitudes canceladas.
* Matches archivados.
* Contactos compartidos.
* Relaciones anteriores.

---

# Reportes

## POST `/reports`

Reporta a otro usuario.

### Autenticación

Requerida.

### Request

```json
{
  "reportedUserId": "4d3d11ad-8cf3-433f-a81e-b222238c9624",
  "reason": "INAPPROPRIATE_BEHAVIOUR",
  "description": "Envió mensajes inapropiados después del match."
}
```

### Response `201`

```json
{
  "success": true,
  "data": {
    "id": "a7103598-f8e6-4ee8-804a-c92b0ef8ca74",
    "status": "OPEN",
    "createdAt": "2026-08-04T10:10:00.000Z"
  }
}
```

### Restricciones

* No permitir reportarse a uno mismo.
* Motivo obligatorio.
* Descripción con longitud máxima.
* No incluir datos innecesariamente sensibles.
* Rate limiting para evitar abuso.

### Errores

```text
CANNOT_REPORT_SELF
USER_NOT_FOUND
INVALID_REPORT_REASON
```

El usuario reportado nunca debe conocer la identidad del reportante a través de la API.

---

# Analítica de producto

## POST `/analytics/events`

Registra eventos permitidos del cliente.

### Autenticación

Opcional.

### Request

```json
{
  "anonymousId": "anon-opaque-id",
  "eventName": "landing_viewed",
  "eventProperties": {
    "source": "qr_gym_centro"
  },
  "sessionId": "session-opaque-id"
}
```

### Response `201`

```json
{
  "success": true,
  "data": null
}
```

### Reglas

* Usar lista blanca de eventos.
* Limitar tamaño de `eventProperties`.
* El servidor establece `userId` si existe sesión.
* No aceptar un `userId` arbitrario del cliente.
* Eliminar propiedades no permitidas.
* No registrar contactos, tokens o contraseñas.
* Aplicar rate limiting.

### Eventos de backend

Los eventos críticos deben registrarse desde el backend, no depender del cliente.

Ejemplos:

```text
signup_completed
match_request_sent
match_request_accepted
contact_revealed
feedback_submitted
user_blocked
user_reported
```

---

# Panel de administración

Todos los endpoints de esta sección requieren rol `ADMIN`.

---

## GET `/admin/metrics/overview`

Devuelve métricas agregadas.

### Autenticación

Requerida.

### Autorización

Solo `ADMIN`.

### Query parameters

```text
from
to
gymId
city
```

### Response `200`

```json
{
  "success": true,
  "data": {
    "period": {
      "from": "2026-07-01T00:00:00.000Z",
      "to": "2026-07-31T23:59:59.999Z"
    },
    "visits": 300,
    "registrations": 42,
    "completedProfiles": 31,
    "requestsSent": 18,
    "requestsAccepted": 8,
    "activeMatches": 8,
    "confirmedWorkouts": 4,
    "repeatIntentionsYes": 3,
    "conversionRates": {
      "visitToRegistration": 14,
      "registrationToCompletedProfile": 73.81,
      "requestToAcceptedMatch": 44.44
    }
  }
}
```

No devolver datos personales salvo que sean estrictamente necesarios.

---

## GET `/admin/reports`

Lista reportes.

### Autenticación

Requerida.

### Autorización

Solo `ADMIN`.

### Query parameters

```text
status
reason
page
pageSize
```

### Response `200`

```json
{
  "success": true,
  "data": [
    {
      "id": "a7103598-f8e6-4ee8-804a-c92b0ef8ca74",
      "reason": "INAPPROPRIATE_BEHAVIOUR",
      "description": "Envió mensajes inapropiados después del match.",
      "status": "OPEN",
      "reporter": {
        "userId": "2d0c10b2-b989-48ac-885e-2fa4020ba83a",
        "displayName": "María"
      },
      "reportedUser": {
        "userId": "4d3d11ad-8cf3-433f-a81e-b222238c9624",
        "displayName": "Carlos",
        "status": "ACTIVE"
      },
      "createdAt": "2026-08-04T10:10:00.000Z"
    }
  ],
  "pagination": {
    "page": 1,
    "pageSize": 20,
    "totalItems": 1,
    "totalPages": 1,
    "hasNextPage": false,
    "hasPreviousPage": false
  }
}
```

---

## GET `/admin/reports/:reportId`

Devuelve el detalle de un reporte.

### Autenticación

Requerida.

### Autorización

Solo `ADMIN`.

### Response `200`

Debe incluir:

* Reporte.
* Usuarios implicados.
* Estado de cuenta.
* Historial de revisión disponible.
* Número agregado de reportes previos.
* Contexto mínimo necesario.

No debe mostrar contraseñas, tokens ni otros secretos.

---

## PATCH `/admin/reports/:reportId`

Actualiza el estado del reporte.

### Autenticación

Requerida.

### Autorización

Solo `ADMIN`.

### Request

```json
{
  "status": "RESOLVED"
}
```

### Response `200`

```json
{
  "success": true,
  "data": {
    "id": "a7103598-f8e6-4ee8-804a-c92b0ef8ca74",
    "status": "RESOLVED",
    "reviewedAt": "2026-08-04T12:00:00.000Z",
    "reviewedBy": "admin-user-uuid"
  }
}
```

---

## PATCH `/admin/users/:userId/status`

Suspende o reactiva una cuenta.

### Autenticación

Requerida.

### Autorización

Solo `ADMIN`.

### Request

```json
{
  "status": "SUSPENDED",
  "reason": "Incumplimiento de las normas de uso."
}
```

### Response `200`

```json
{
  "success": true,
  "data": {
    "userId": "4d3d11ad-8cf3-433f-a81e-b222238c9624",
    "status": "SUSPENDED"
  }
}
```

### Comportamiento al suspender

* Invalidar sesiones.
* Excluir del matching.
* Ocultar perfil.
* Cancelar solicitudes pendientes.
* Dejar de exponer contacto.
* Mantener evidencia necesaria para revisión.

No permitir que un administrador se suspenda a sí mismo sin una política específica.

---

## GET `/admin/gyms`

Lista gimnasios para moderación.

### Autenticación

Requerida.

### Autorización

Solo `ADMIN`.

### Query parameters

```text
verificationStatus
city
query
page
pageSize
```

---

## PATCH `/admin/gyms/:gymId`

Modifica y verifica un gimnasio.

### Autenticación

Requerida.

### Autorización

Solo `ADMIN`.

### Request

```json
{
  "name": "Gym Centro Huelva",
  "address": "Calle Ejemplo 10",
  "postalCode": "21001",
  "verificationStatus": "VERIFIED"
}
```

### Response `200`

```json
{
  "success": true,
  "data": {
    "id": "d3921e30-73aa-4f98-bab6-93d203084817",
    "name": "Gym Centro Huelva",
    "verificationStatus": "VERIFIED"
  }
}
```

---

# Paginación

Los endpoints de listado deben utilizar:

```text
page
pageSize
```

## Valores por defecto

```text
page = 1
pageSize = 20
```

## Límites

```text
page >= 1
1 <= pageSize <= 50
```

## Orden estable

Toda paginación debe incluir un orden determinista.

Ejemplo:

```text
ORDER BY created_at DESC, id DESC
```

Para grandes volúmenes futuros se podrá migrar a paginación por cursor.

---

# Filtros y ordenación

Solo deben permitirse campos definidos explícitamente.

Nunca construir dinámicamente nombres de columna directamente desde la entrada del cliente.

Ejemplo permitido:

```text
sort=createdAt
order=desc
```

El backend debe mapear estos valores a una lista blanca interna.

---

# Fechas y horas

Reglas:

* Guardar timestamps en UTC.
* Devolver timestamps en ISO 8601.
* Las disponibilidades locales deben incluir zona horaria.
* El frontend convierte las fechas para presentación.
* No depender de la zona horaria del servidor.
* `birthDate` se representa como fecha sin hora.
* Las horas habituales pueden representarse como `HH:mm`.

---

# Enumeraciones

Los valores enviados deben coincidir exactamente con las enumeraciones documentadas.

Ejemplo:

```text
INTERMEDIATE
STRENGTH
NEED_SPOTTER
```

No aceptar variantes libres como:

```text
intermedio
Strength
spotter
```

El frontend puede traducir las etiquetas visuales, pero la API utiliza identificadores estables.

---

# Idempotencia

Las operaciones críticas deben evitar duplicados incluso cuando el cliente repita una petición.

Especialmente:

* Registro.
* Envío de solicitudes.
* Aceptación de solicitudes.
* Creación de match.
* Envío de feedback.
* Bloqueo de usuario.

La base de datos debe reforzar la idempotencia con restricciones únicas.

Para futuras integraciones puede añadirse:

```text
Idempotency-Key
```

---

# Rate limiting

Aplicar límites específicos.

## Autenticación

Límite estricto para:

```text
/auth/login
/auth/register
/auth/forgot-password
/auth/reset-password
```

## Interacciones

Límite por usuario para:

```text
POST /match-requests
POST /blocks
POST /reports
POST /analytics/events
```

## Respuesta de límite

```json
{
  "success": false,
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Has realizado demasiadas solicitudes. Inténtalo más tarde.",
    "details": null
  }
}
```

Incluir cabeceras estándar cuando sea posible:

```text
Retry-After
RateLimit-Limit
RateLimit-Remaining
RateLimit-Reset
```

---

# Validación

Usar un sistema centralizado de esquemas.

Opciones aceptables:

* Zod.
* Valibot.
* Joi.
* Librería equivalente ya presente.

Cada endpoint debe validar:

* Body.
* Query parameters.
* Route parameters.
* Tipos.
* Longitudes.
* Enumeraciones.
* Formatos.
* Reglas semánticas.

La validación del frontend no sustituye la del backend.

---

# Sanitización

No se debe modificar arbitrariamente el texto del usuario, pero sí:

* Limitar longitud.
* Rechazar caracteres de control no válidos.
* Escapar contenido al mostrarlo.
* Evitar renderizado HTML sin sanitización.
* Normalizar correos.
* Normalizar nombres de gimnasio.
* Usar consultas parametrizadas mediante Prisma.

---

# Privacidad de campos

## Nunca devolver en ninguna respuesta pública

```text
passwordHash
resetToken
sessionToken
internalNotes
deletedAt
securityLogs
```

## Solo devolver al propietario

```text
email
birthDate
contactType
contactValue
accountStatus
lastLoginAt
```

## Solo devolver tras match autorizado

```text
contactType
contactValue
```

## Solo administradores

```text
reporterIdentity
moderationHistory
suspensionReason
```

---

# Errores estándar

## Autenticación y autorización

```text
UNAUTHENTICATED
FORBIDDEN
INVALID_CREDENTIALS
ACCOUNT_SUSPENDED
ACCOUNT_DELETED
```

## Validación

```text
VALIDATION_ERROR
INVALID_ENUM_VALUE
INVALID_DATE
INVALID_TIME_RANGE
INVALID_IDENTIFIER
```

## Cuenta y perfil

```text
EMAIL_ALREADY_REGISTERED
PROFILE_NOT_FOUND
PROFILE_ALREADY_EXISTS
PROFILE_INCOMPLETE
PROFILE_NOT_VISIBLE
UNDERAGE_USER
```

## Gimnasios

```text
GYM_NOT_FOUND
GYM_ALREADY_EXISTS
USER_GYM_ALREADY_EXISTS
```

## Matching

```text
NO_COMPATIBLE_CANDIDATES
NO_SHARED_GYM
NO_SHARED_AVAILABILITY
MATCH_REQUEST_ALREADY_EXISTS
MATCH_REQUEST_NOT_FOUND
MATCH_REQUEST_NOT_PENDING
MATCH_REQUEST_EXPIRED
MATCH_ALREADY_EXISTS
MATCH_NOT_FOUND
NOT_MATCH_PARTICIPANT
NOT_REQUEST_RECEIVER
NOT_REQUEST_SENDER
RECENTLY_REJECTED
```

## Seguridad y moderación

```text
USER_BLOCKED
USER_ALREADY_BLOCKED
CANNOT_BLOCK_SELF
CANNOT_REPORT_SELF
REPORT_NOT_FOUND
RATE_LIMIT_EXCEEDED
```

## Genéricos

```text
RESOURCE_NOT_FOUND
CONFLICT
INTERNAL_ERROR
SERVICE_UNAVAILABLE
```

---

# Errores internos

El cliente nunca debe recibir:

* Stack traces.
* Consultas SQL.
* Variables de entorno.
* Rutas internas del servidor.
* Nombres de hosts.
* Credenciales.
* Mensajes crudos de Prisma.
* Detalles de proveedores externos.

Ejemplo incorrecto:

```json
{
  "error": "PrismaClientKnownRequestError P2002..."
}
```

Ejemplo correcto:

```json
{
  "success": false,
  "error": {
    "code": "EMAIL_ALREADY_REGISTERED",
    "message": "Ya existe una cuenta con ese correo.",
    "details": null
  }
}
```

---

# Transacciones obligatorias

Usar transacciones en operaciones con múltiples cambios relacionados.

## Aceptar una solicitud

Debe incluir:

* Actualizar solicitud.
* Crear match.
* Registrar fecha de contacto.
* Registrar analítica.

## Bloquear usuario

Debe incluir:

* Crear bloqueo.
* Cancelar solicitudes.
* Actualizar matches.
* Invalidar acceso al contacto.

## Eliminar cuenta

Debe incluir:

* Actualizar estado.
* Ocultar perfil.
* Cancelar solicitudes.
* Invalidar sesiones.

## Cambiar gimnasio principal

Debe incluir:

* Desactivar gimnasio principal anterior.
* Activar el nuevo.

---

# Concurrencia

Las operaciones críticas deben tolerar peticiones simultáneas.

Casos importantes:

* Dos usuarios intentan enviarse una solicitud simultáneamente.
* Una solicitud se acepta dos veces.
* Una solicitud se acepta mientras el emisor la cancela.
* Se bloquea a un usuario durante la aceptación.
* Dos peticiones intentan crear el mismo match.

Usar:

* Restricciones únicas.
* Transacciones.
* Comprobaciones dentro de la transacción.
* Manejo explícito de conflictos.

---

# Contratos TypeScript

La implementación debe generar o mantener tipos compartidos.

Ejemplo:

```typescript
export interface ApiSuccessResponse<T> {
  success: true;
  data: T;
}

export interface ApiErrorDetail {
  code: string;
  message: string;
  details: Record<string, string[]> | null;
}

export interface ApiErrorResponse {
  success: false;
  error: ApiErrorDetail;
}

export type ApiResponse<T> =
  | ApiSuccessResponse<T>
  | ApiErrorResponse;
```

No duplicar manualmente tipos inconsistentes entre frontend y backend.

---

# OpenAPI

Lovable debe generar una especificación OpenAPI cuando la estructura del proyecto lo permita.

Ruta recomendada:

```text
docs/openapi.yaml
```

Debe documentar:

* Endpoints.
* Parámetros.
* Schemas.
* Respuestas.
* Errores.
* Autenticación.
* Enumeraciones.

La documentación OpenAPI no sustituye este archivo; ambos deben mantenerse alineados.

---

# Orden de implementación

Lovable debe implementar la API en este orden:

1. Respuestas y errores comunes.
2. Validación.
3. Autenticación.
4. Cuenta.
5. Perfil.
6. Gimnasios.
7. Disponibilidad.
8. Matching.
9. Solicitudes.
10. Matches.
11. Feedback.
12. Bloqueos y reportes.
13. Analítica.
14. Administración.
15. OpenAPI.
16. Tests de integración.

---

# Tests mínimos

Cada endpoint crítico debe incluir tests.

## Autenticación

* Registro correcto.
* Correo duplicado.
* Login correcto.
* Credenciales incorrectas.
* Cuenta suspendida.
* Logout.

## Perfil

* Crear perfil.
* Impedir segundo perfil.
* Rechazar menor de edad.
* Actualizar solo perfil propio.
* Ocultar contacto en perfil público.

## Matching

* Incluir candidato compatible.
* Excluir usuario actual.
* Excluir perfil oculto.
* Excluir usuario bloqueado.
* Excluir sin gimnasio compartido.
* Excluir sin horario compatible.

## Solicitudes

* Crear solicitud.
* Impedir duplicado.
* Impedir solicitud propia.
* Aceptar como receptor.
* Impedir aceptar como emisor.
* Crear match una sola vez.
* Rechazar.
* Cancelar.

## Contactos

* No mostrar antes del match.
* Mostrar a participantes de match activo.
* No mostrar a terceros.
* Ocultar tras bloqueo.

## Feedback

* Crear feedback.
* Impedir duplicado.
* Impedir feedback de usuario ajeno al match.

## Administración

* Impedir acceso a usuario normal.
* Permitir acceso a administrador.
* Suspender cuenta.
* Resolver reporte.

---

# Criterios de aceptación

La API se considera correctamente implementada cuando:

* Todos los endpoints privados requieren sesión.
* La autorización se aplica en el backend.
* Los contratos de respuesta son consistentes.
* Los errores no exponen información interna.
* Los contactos solo se muestran tras un match autorizado.
* El matching excluye perfiles no elegibles.
* No se pueden crear solicitudes o matches duplicados.
* Las operaciones críticas usan transacciones.
* Las entradas se validan con esquemas.
* Los listados están paginados.
* Existe rate limiting en rutas sensibles.
* Los eventos críticos se registran desde el servidor.
* Los tests cubren los flujos principales.
* La API no depende de Supabase ni Firebase.
* El acceso a PostgreSQL se realiza exclusivamente desde el backend mediante Prisma.
