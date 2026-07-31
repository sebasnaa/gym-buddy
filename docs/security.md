# GymBuddy — Security Specification

Version: 0.1

---

# Objetivo

Este documento define todas las reglas de seguridad del proyecto.

Su objetivo es proteger:

* Usuarios.
* Datos personales.
* Datos de contacto.
* Credenciales.
* API.
* Base de datos.
* Infraestructura.

Este documento tiene prioridad sobre cualquier otra especificación.

Si existe conflicto entre funcionalidad y seguridad, prevalece la seguridad.

---

# Principios

GymBuddy debe seguir estos principios:

* Secure by Default
* Least Privilege
* Defense in Depth
* Zero Trust
* Fail Secure
* Privacy by Design
* Data Minimization

---

# Arquitectura de seguridad

```text
Browser

↓

HTTPS

↓

Frontend React

↓

Backend API

↓

Authentication

↓

Authorization

↓

Business Rules

↓

Repositories

↓

Prisma

↓

PostgreSQL
```

El navegador nunca accede directamente a PostgreSQL.

---

# Comunicación

Toda comunicación debe realizarse mediante HTTPS.

Nunca permitir:

* HTTP
* certificados inválidos
* contenido mixto (Mixed Content)

---

# Autenticación

La autenticación será mediante sesiones.

No almacenar:

* passwords
* tokens
* sesiones

en LocalStorage.

Preferencia:

Cookies

* HttpOnly
* Secure
* SameSite=Lax (o Strict cuando sea posible)

---

# Contraseñas

Nunca guardar contraseñas.

Guardar únicamente:

```text
password_hash
```

Usar Argon2id.

Si no es posible:

bcrypt con coste adecuado.

Nunca usar:

* MD5
* SHA1
* SHA256 sin salt
* Base64

---

# Política de contraseña

Mínimo:

10 caracteres

Recomendado:

12+

Aceptar passphrases.

No exigir reglas absurdas como:

* un símbolo
* una mayúscula
* un número

si la contraseña ya tiene suficiente entropía.

Bloquear contraseñas comunes.

---

# Recuperación

Los tokens de recuperación:

* aleatorios
* de un solo uso
* con expiración
* almacenados mediante hash

Nunca guardar el token original.

---

# Sesiones

La sesión debe invalidarse cuando:

* logout
* cambio de contraseña
* suspensión
* eliminación de cuenta

---

# Autorización

Nunca confiar en:

```json
{
    "userId": "...",
    "role": "ADMIN"
}
```

enviado por el cliente.

El usuario autenticado siempre debe obtenerse desde la sesión.

---

# Roles

MVP

USER

ADMIN

Nada más.

---

# Comprobaciones

Cada endpoint privado debe verificar:

* usuario autenticado
* cuenta activa
* permisos
* propietario del recurso

---

# Protección CSRF

Si se usan cookies:

proteger:

POST

PATCH

PUT

DELETE

---

# Rate limiting

Aplicar especialmente sobre:

* login
* register
* forgot password
* reset password
* report
* block
* match request

---

# SQL Injection

Nunca construir consultas manuales.

Siempre utilizar Prisma.

Nunca:

```sql
SELECT * FROM users WHERE email='${email}'
```

---

# XSS

Nunca renderizar HTML enviado por usuarios.

Escapar siempre.

No utilizar:

dangerouslySetInnerHTML

salvo que exista sanitización estricta.

---

# Sanitización

Limitar:

* longitud
* caracteres inválidos

No modificar el contenido salvo normalizaciones necesarias.

---

# Uploads

Solo permitir imágenes.

Validar:

* MIME
* tamaño
* extensión
* contenido

Renombrar archivos.

Nunca utilizar el nombre enviado por el usuario.

---

# EXIF

Eliminar metadatos cuando sea posible.

---

# Datos sensibles

Nunca registrar:

* password
* hash
* contacto
* email completo
* cookies
* tokens

---

# Logs

Registrar:

* errores
* excepciones
* acciones críticas

No registrar información privada.

---

# Información personal

El contacto:

```text
contact_type

contact_value
```

es información privada.

Solo puede mostrarse cuando:

* existe match
* match activo
* ambos usuarios pertenecen al match

Nunca antes.

---

# Privacidad

Nunca devolver:

password_hash

email

tokens

last_login

deleted_at

internal_notes

---

# Enumeración de usuarios

El login debe devolver siempre:

```text
INVALID_CREDENTIALS
```

Nunca indicar:

* correo inexistente
* contraseña incorrecta

---

# IDs

Todos los IDs serán UUID.

Nunca IDs incrementales.

---

# Base de datos

No utilizar:

ON DELETE CASCADE

cuando pueda destruir información histórica importante.

Preferir:

Soft Delete.

---

# Eliminación

Eliminar una cuenta significa:

* ocultarla
* cancelar solicitudes
* ocultar contacto
* invalidar sesiones

No borrar datos críticos para auditoría.

---

# Bloqueos

Un usuario bloqueado:

* desaparece del matching
* desaparece de búsquedas
* no puede enviar solicitudes
* no puede recibir solicitudes
* pierde acceso al contacto

---

# Reportes

Los reportes son privados.

El usuario reportado nunca conocerá:

* quién reportó
* cuándo
* contenido interno de revisión

---

# Headers HTTP

Configurar:

Strict-Transport-Security

X-Content-Type-Options

Referrer-Policy

Content-Security-Policy

X-Frame-Options

Permissions-Policy

---

# Content Security Policy

Evitar:

inline scripts

eval()

dominios desconocidos

---

# Dependencias

Actualizar periódicamente.

Eliminar dependencias abandonadas.

No instalar librerías innecesarias.

---

# Variables de entorno

Nunca subir:

.env

al repositorio.

Utilizar:

.env.example

---

# Secretos

Guardar únicamente en variables de entorno.

Nunca:

API Keys

JWT Secret

DATABASE_URL

en el código.

---

# Errores

Nunca devolver:

Stack Trace

Prisma Errors

SQL

Filesystem Paths

Variables de entorno

---

# Auditoría

Registrar:

* login
* logout
* cambio contraseña
* bloqueo
* reporte
* suspensión
* eliminación

---

# Backups

La base de datos debe tener backups periódicos.

Los backups deben cifrarse.

---

# Cifrado

TLS para comunicaciones.

Hash para contraseñas.

No inventar algoritmos propios.

---

# Dependencias externas

Evaluar antes de instalar.

Evitar SDKs innecesarios.

---

# GDPR

El usuario podrá:

* eliminar cuenta
* modificar datos
* descargar datos (futuro)

---

# Principio de mínima información

Mostrar únicamente los datos necesarios.

Ejemplo:

En el matching NO mostrar:

* teléfono
* email
* Instagram
* Telegram

Hasta que exista un match.

---

# Protección frente a abuso

Limitar:

* solicitudes por minuto
* reportes
* bloqueos
* registros
* logins

---

# Seguridad del matching

No permitir:

Usuario A

↓

Usuario A

Tampoco:

Solicitudes duplicadas.

Matches duplicados.

Solicitudes cruzadas.

---

# Seguridad del administrador

Las rutas /admin:

Solo ADMIN.

Nunca USER.

---

# Validación

Toda validación debe existir:

Frontend

↓

Backend

Nunca confiar solo en React.

---

# Reglas para Lovable

Lovable nunca debe:

* guardar passwords
* guardar tokens inseguros
* usar LocalStorage para autenticación
* exponer contacto antes del match
* devolver información privada
* acceder a PostgreSQL desde React
* construir SQL manualmente
* desactivar validaciones
* eliminar restricciones de seguridad
* desactivar autorización
* ignorar este documento por simplicidad

---

# Checklist de seguridad

Antes de dar una funcionalidad por terminada comprobar:

☐ Usa HTTPS

☐ Requiere autenticación cuando corresponde

☐ Comprueba autorización

☐ Valida entradas

☐ Sanitiza datos

☐ No expone información privada

☐ Usa Prisma

☐ No construye SQL

☐ Tiene rate limiting

☐ Registra auditoría

☐ Maneja errores correctamente

☐ No devuelve información sensible

☐ Respeta las reglas de privacidad

☐ Cumple este documento
