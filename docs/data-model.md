# GymBuddy — Data Model

Version: 0.1
Database: PostgreSQL
ORM: Prisma

---

# Objetivo

Definir el modelo de datos del MVP de GymBuddy.

Este documento es la fuente de verdad para:

* Tablas.
* Relaciones.
* Restricciones.
* Índices.
* Estados.
* Reglas de integridad.
* Generación del esquema de Prisma.
* Migraciones de PostgreSQL.

Lovable debe revisar este documento antes de modificar el esquema de la base de datos.

---

# Principios generales

El modelo debe cumplir estas reglas:

* Usar UUID como identificador principal.
* Usar nombres de tablas y campos consistentes.
* Incluir `created_at` y `updated_at` cuando corresponda.
* Usar claves foráneas.
* Añadir restricciones únicas.
* Añadir índices en campos usados para búsquedas.
* No guardar datos derivados cuando puedan calcularse fácilmente.
* No exponer datos de contacto sin autorización.
* No eliminar físicamente usuarios durante el MVP salvo necesidad técnica.
* No permitir duplicidades lógicas.
* No permitir relaciones de un usuario consigo mismo.

---

# Convenciones

## Identificadores

Todas las tablas principales deben usar:

```text
id UUID PRIMARY KEY
```

Preferiblemente generado por PostgreSQL o Prisma.

## Fechas

Usar:

```text
created_at TIMESTAMP WITH TIME ZONE
updated_at TIMESTAMP WITH TIME ZONE
```

## Estados

Usar enums cuando el conjunto de valores sea cerrado y estable.

## Borrado lógico

Para usuarios y perfiles, preferir:

```text
status
deleted_at
```

en lugar de eliminar registros físicamente.

---

# Enumeraciones

## UserRole

```text
USER
ADMIN
```

## UserStatus

```text
ACTIVE
SUSPENDED
DELETED
```

## ExperienceLevel

```text
BEGINNER
INTERMEDIATE
ADVANCED
```

## TrainingGoal

```text
MUSCLE_GAIN
FAT_LOSS
STRENGTH
POWERLIFTING
GENERAL_FITNESS
CROSS_TRAINING
RETURN_TO_TRAINING
OTHER
```

## BuddyReason

```text
NEW_TO_GYM
EMBARRASSED_TO_TRAIN_ALONE
NEED_MOTIVATION
NEED_SPOTTER
TRAVELLING
RECENTLY_MOVED
FRIENDS_DO_NOT_TRAIN
SCHEDULE_DOES_NOT_MATCH_FRIENDS
OTHER
```

## TrainingPreference

```text
ALWAYS_WITH_PARTNER
SOMETIMES_WITH_PARTNER
ONLY_FOR_SPECIFIC_WORKOUTS
USUALLY_ALONE
```

## ContactType

```text
WHATSAPP
TELEGRAM
INSTAGRAM
PHONE
OTHER
```

## MatchRequestStatus

```text
PENDING
ACCEPTED
REJECTED
CANCELLED
EXPIRED
```

## MatchStatus

```text
ACTIVE
ARCHIVED
BLOCKED
CANCELLED
```

## WorkoutHappened

```text
YES
NO
PENDING
```

## RepeatIntention

```text
YES
NO
MAYBE
```

## ReportStatus

```text
OPEN
REVIEWING
RESOLVED
DISMISSED
```

## ReportReason

```text
INAPPROPRIATE_BEHAVIOUR
HARASSMENT
FAKE_PROFILE
SPAM
UNSAFE_BEHAVIOUR
OTHER
```

## GymVerificationStatus

```text
UNVERIFIED
VERIFIED
REJECTED
```

---

# Tabla users

Representa la cuenta de acceso.

## Campos

```text
id
email
password_hash
role
status
email_verified_at
last_login_at
created_at
updated_at
deleted_at
```

## Definición

### id

UUID.

Clave primaria.

### email

Correo electrónico del usuario.

Debe almacenarse normalizado en minúsculas.

Debe ser único.

### password_hash

Hash seguro de la contraseña.

Nunca almacenar contraseñas en texto plano.

### role

Enum `UserRole`.

Valor por defecto:

```text
USER
```

### status

Enum `UserStatus`.

Valor por defecto:

```text
ACTIVE
```

### email_verified_at

Opcional en el MVP.

Puede quedar preparado aunque la verificación por correo no se implemente inicialmente.

### last_login_at

Fecha del último inicio de sesión correcto.

### created_at

Fecha de creación.

### updated_at

Fecha de última modificación.

### deleted_at

Opcional.

Se utilizará para borrado lógico.

## Relaciones

```text
users 1 — 1 profiles
users 1 — N user_gyms
users 1 — N availability
users 1 — N strength_marks
users 1 — N match_requests enviadas
users 1 — N match_requests recibidas
users 1 — N matches
users 1 — N workout_feedback
users 1 — N analytics_events
```

## Restricciones

* `email` debe ser único.
* `email` no puede ser nulo.
* `password_hash` no puede ser nulo.
* Un usuario eliminado no debe poder iniciar sesión.

## Índices

* Índice único en `email`.
* Índice en `status`.
* Índice en `created_at`.

---

# Tabla profiles

Representa la información pública y deportiva del usuario.

## Campos

```text
id
user_id
display_name
birth_date
gender
city
bio
photo_url
experience_level
primary_goal
buddy_reason
training_preference
contact_type
contact_value
profile_completed
visibility_enabled
created_at
updated_at
```

## Definición

### id

UUID.

Clave primaria.

### user_id

Clave foránea a `users.id`.

Debe ser único.

### display_name

Nombre visible.

No debe requerir nombre completo real.

### birth_date

Fecha de nacimiento.

El sistema debe impedir el registro de menores de 18 años.

### gender

Opcional.

No debe formar parte obligatoria del matching inicial.

### city

Ciudad principal.

Para el MVP se guardará como texto.

### bio

Descripción breve.

Longitud recomendada máxima:

```text
300 caracteres
```

### photo_url

URL de imagen de perfil.

Opcional.

### experience_level

Enum `ExperienceLevel`.

### primary_goal

Enum `TrainingGoal`.

### buddy_reason

Enum `BuddyReason`.

### training_preference

Enum `TrainingPreference`.

### contact_type

Enum `ContactType`.

Opcional hasta completar el perfil.

### contact_value

Dato de contacto.

Debe ocultarse en todas las respuestas públicas.

Solo debe mostrarse cuando exista un match activo y autorizado.

### profile_completed

Boolean.

Valor por defecto:

```text
false
```

### visibility_enabled

Boolean.

Permite al usuario ocultarse temporalmente de los resultados.

Valor por defecto:

```text
true
```

## Relaciones

```text
profiles N — 1 users
```

## Restricciones

* Un usuario solo puede tener un perfil.
* `display_name` obligatorio.
* `birth_date` obligatorio.
* El usuario debe ser mayor de edad.
* `contact_value` no debe incluirse en endpoints públicos.
* `bio` debe tener longitud máxima.
* `profile_completed` solo debe ser `true` cuando existan los campos mínimos.

## Campos mínimos para considerar perfil completo

* `display_name`
* `birth_date`
* `city`
* `experience_level`
* `primary_goal`
* `buddy_reason`
* `training_preference`
* gimnasio principal
* al menos una disponibilidad
* `contact_type`
* `contact_value`

## Índices

* Índice único en `user_id`.
* Índice en `city`.
* Índice en `experience_level`.
* Índice en `primary_goal`.
* Índice en `profile_completed`.
* Índice en `visibility_enabled`.

---

# Tabla gyms

Representa un gimnasio.

## Campos

```text
id
name
normalized_name
city
address
postal_code
verification_status
created_by
created_at
updated_at
```

## Definición

### id

UUID.

### name

Nombre visible del gimnasio.

### normalized_name

Versión normalizada para evitar duplicados.

Ejemplo:

```text
Basic Fit Huelva Centro
```

podría normalizarse como:

```text
basic-fit-huelva-centro
```

### city

Ciudad.

### address

Opcional.

### postal_code

Opcional.

### verification_status

Enum `GymVerificationStatus`.

Valor por defecto:

```text
UNVERIFIED
```

### created_by

Usuario que añadió el gimnasio.

Puede ser nulo si se trata de un gimnasio precargado.

## Relaciones

```text
gyms 1 — N user_gyms
gyms 1 — N match_requests
gyms 1 — N matches
```

## Restricciones

* Evitar duplicados por nombre normalizado y ciudad.
* `name` obligatorio.
* `city` obligatorio.

## Índices

* Índice compuesto único en `normalized_name + city`.
* Índice en `city`.
* Índice en `verification_status`.

---

# Tabla user_gyms

Relaciona usuarios y gimnasios.

Permite soportar más de un gimnasio por usuario.

## Campos

```text
id
user_id
gym_id
is_primary
joined_at
created_at
```

## Definición

### user_id

Clave foránea a `users.id`.

### gym_id

Clave foránea a `gyms.id`.

### is_primary

Indica el gimnasio principal.

Valor por defecto:

```text
false
```

### joined_at

Fecha aproximada en la que el usuario comenzó a entrenar allí.

Opcional.

## Relaciones

```text
user_gyms N — 1 users
user_gyms N — 1 gyms
```

## Restricciones

* Un usuario no puede tener el mismo gimnasio duplicado.
* Solo debe existir un gimnasio principal por usuario.
* Para aparecer en matching, el usuario debe tener al menos un gimnasio.

## Índices

* Índice único en `user_id + gym_id`.
* Índice en `gym_id`.
* Índice en `user_id`.
* Índice compuesto en `user_id + is_primary`.

---

# Tabla availability

Representa los días y franjas horarias habituales.

## Campos

```text
id
user_id
day_of_week
start_time
end_time
timezone
created_at
updated_at
```

## Definición

### day_of_week

Entero entre 1 y 7.

Convención:

```text
1 = lunes
2 = martes
3 = miércoles
4 = jueves
5 = viernes
6 = sábado
7 = domingo
```

### start_time

Hora de inicio.

### end_time

Hora de fin.

### timezone

Zona horaria.

Valor por defecto para la primera validación:

```text
Europe/Madrid
```

## Restricciones

* `day_of_week` entre 1 y 7.
* `start_time` debe ser anterior a `end_time`.
* Evitar duplicados exactos.
* Permitir varias franjas para un mismo día.

## Índices

* Índice en `user_id`.
* Índice en `day_of_week`.
* Índice compuesto en `user_id + day_of_week`.
* Índice compuesto en `day_of_week + start_time + end_time`.

---

# Tabla strength_marks

Representa marcas deportivas opcionales.

No deben ser obligatorias para completar el perfil.

## Campos

```text
id
user_id
exercise
weight_kg
repetitions
estimated_one_rep_max
recorded_at
created_at
updated_at
```

## Definición

### exercise

Nombre normalizado del ejercicio.

Valores iniciales recomendados:

```text
BENCH_PRESS
SQUAT
DEADLIFT
OVERHEAD_PRESS
OTHER
```

### weight_kg

Peso levantado.

### repetitions

Número de repeticiones.

### estimated_one_rep_max

Valor calculado opcional.

No debe confiarse ciegamente en este dato para el matching.

## Restricciones

* `weight_kg` mayor que cero.
* `repetitions` mayor que cero.
* No exigir marcas a principiantes.
* No usar marcas como filtro obligatorio.

## Índices

* Índice en `user_id`.
* Índice compuesto en `user_id + exercise`.

---

# Tabla match_requests

Representa una solicitud de conexión.

## Campos

```text
id
sender_id
receiver_id
gym_id
compatibility_score
status
message
created_at
responded_at
expires_at
updated_at
```

## Definición

### sender_id

Usuario que envía la solicitud.

### receiver_id

Usuario que recibe la solicitud.

### gym_id

Gimnasio sobre el que se propone entrenar.

### compatibility_score

Puntuación entre 0 y 100.

Debe guardarse como entero.

### status

Enum `MatchRequestStatus`.

Valor por defecto:

```text
PENDING
```

### message

Mensaje opcional y breve.

Longitud máxima recomendada:

```text
250 caracteres
```

### expires_at

Opcional.

Puede usarse para expirar solicitudes antiguas.

## Relaciones

```text
match_requests N — 1 users como sender
match_requests N — 1 users como receiver
match_requests N — 1 gyms
match_requests 1 — 0..1 matches
```

## Restricciones

* `sender_id` no puede ser igual a `receiver_id`.
* No permitir más de una solicitud pendiente entre la misma pareja y gimnasio.
* No permitir solicitudes a usuarios bloqueados.
* No permitir solicitudes a perfiles ocultos o incompletos.
* Solo el receptor puede aceptar o rechazar.
* Solo el emisor puede cancelar.

## Duplicidad lógica

Debe evitarse que existan simultáneamente:

```text
A → B
B → A
```

como solicitudes pendientes para el mismo gimnasio.

La pareja debe normalizarse en lógica de negocio o mediante campos auxiliares.

## Índices

* Índice en `sender_id`.
* Índice en `receiver_id`.
* Índice en `gym_id`.
* Índice en `status`.
* Índice compuesto en `receiver_id + status`.
* Índice compuesto en `sender_id + status`.
* Índice compuesto en `sender_id + receiver_id + gym_id`.

---

# Tabla matches

Representa una conexión aceptada.

## Campos

```text
id
user_one_id
user_two_id
gym_id
match_request_id
status
contact_revealed_at
planned_workout_at
last_interaction_at
created_at
updated_at
```

## Definición

### user_one_id

Primer usuario de la pareja.

### user_two_id

Segundo usuario.

Para evitar duplicados, deben almacenarse en orden determinista.

Ejemplo:

```text
user_one_id = UUID menor
user_two_id = UUID mayor
```

### gym_id

Gimnasio asociado al match.

### match_request_id

Solicitud que originó el match.

### status

Enum `MatchStatus`.

Valor por defecto:

```text
ACTIVE
```

### contact_revealed_at

Fecha en la que se permitió mostrar el contacto.

### planned_workout_at

Fecha tentativa del entrenamiento.

Opcional.

### last_interaction_at

Fecha de la última interacción relevante.

## Relaciones

```text
matches N — 1 users como user_one
matches N — 1 users como user_two
matches N — 1 gyms
matches 1 — 1 match_requests
matches 1 — N workout_feedback
```

## Restricciones

* `user_one_id` no puede ser igual a `user_two_id`.
* Un match solo puede crearse desde una solicitud aceptada.
* Una solicitud solo puede originar un match.
* No permitir matches duplicados entre la misma pareja y gimnasio.
* El contacto solo debe revelarse si el match está activo.
* Un bloqueo posterior debe cambiar el estado del match.

## Índices

* Índice único en `match_request_id`.
* Índice compuesto único en `user_one_id + user_two_id + gym_id`.
* Índice en `user_one_id`.
* Índice en `user_two_id`.
* Índice en `gym_id`.
* Índice en `status`.

---

# Tabla workout_feedback

Representa la respuesta posterior al posible entrenamiento.

Cada usuario puede dejar una respuesta por match.

## Campos

```text
id
match_id
user_id
workout_happened
would_repeat
rating
comment
submitted_at
created_at
updated_at
```

## Definición

### workout_happened

Enum `WorkoutHappened`.

### would_repeat

Enum `RepeatIntention`.

Puede ser nulo si el entrenamiento no ocurrió.

### rating

Número entero entre 1 y 5.

Opcional.

### comment

Comentario opcional.

Longitud máxima recomendada:

```text
500 caracteres
```

## Restricciones

* Un usuario solo puede enviar un feedback por match.
* El usuario debe pertenecer al match.
* Si `workout_happened = NO`, `would_repeat` puede quedar nulo.
* `rating` debe estar entre 1 y 5.

## Índices

* Índice único en `match_id + user_id`.
* Índice en `match_id`.
* Índice en `user_id`.
* Índice en `workout_happened`.
* Índice en `would_repeat`.

---

# Tabla blocked_users

Representa un bloqueo entre usuarios.

## Campos

```text
id
blocker_id
blocked_id
reason
created_at
```

## Definición

### blocker_id

Usuario que bloquea.

### blocked_id

Usuario bloqueado.

### reason

Texto opcional interno.

## Restricciones

* Un usuario no puede bloquearse a sí mismo.
* No permitir bloqueos duplicados.
* El bloqueo debe ocultar ambos perfiles entre sí.
* Debe impedir nuevas solicitudes.
* Debe impedir revelar datos de contacto.
* Puede archivar matches activos existentes.

## Índices

* Índice único en `blocker_id + blocked_id`.
* Índice en `blocker_id`.
* Índice en `blocked_id`.

---

# Tabla reports

Representa un reporte sobre otro usuario.

## Campos

```text
id
reporter_id
reported_user_id
reason
description
status
reviewed_by
reviewed_at
created_at
updated_at
```

## Definición

### reporter_id

Usuario que reporta.

### reported_user_id

Usuario reportado.

### reason

Enum `ReportReason`.

### description

Detalle opcional.

### status

Enum `ReportStatus`.

Valor por defecto:

```text
OPEN
```

### reviewed_by

Administrador que revisó el reporte.

Opcional.

## Restricciones

* Un usuario no puede reportarse a sí mismo.
* El usuario reportado debe existir.
* Solo administradores pueden modificar el estado.
* No mostrar al usuario reportado quién lo reportó.

## Índices

* Índice en `reported_user_id`.
* Índice en `reporter_id`.
* Índice en `status`.
* Índice en `created_at`.

---

# Tabla analytics_events

Representa eventos del embudo de producto.

## Campos

```text
id
user_id
anonymous_id
event_name
event_properties
session_id
created_at
```

## Definición

### user_id

Opcional.

Se usa cuando el usuario está autenticado.

### anonymous_id

Identificador anónimo previo al registro.

### event_name

Nombre del evento.

### event_properties

JSONB.

Ejemplo:

```json
{
  "gymId": "uuid",
  "compatibilityScore": 82,
  "source": "landing"
}
```

### session_id

Identificador de sesión analítica.

Opcional.

## Restricciones

* Debe existir `user_id` o `anonymous_id`.
* No guardar contraseñas.
* No guardar tokens.
* No guardar datos de contacto.
* No guardar información médica.

## Índices

* Índice en `event_name`.
* Índice en `user_id`.
* Índice en `anonymous_id`.
* Índice en `created_at`.
* Índice compuesto en `event_name + created_at`.
* Índice GIN en `event_properties` solo si resulta necesario.

---

# Eventos de analítica permitidos

```text
landing_viewed
signup_started
signup_completed
login_completed
profile_started
profile_completed
gym_selected
availability_added
match_search_started
match_results_viewed
match_request_sent
match_request_accepted
match_request_rejected
match_request_cancelled
contact_revealed
workout_planned
workout_confirmed
workout_not_completed
repeat_intention_yes
repeat_intention_no
feedback_submitted
user_blocked
user_reported
```

---

# Relaciones principales

```text
users
  ├── profiles
  ├── user_gyms
  ├── availability
  ├── strength_marks
  ├── match_requests
  ├── matches
  ├── workout_feedback
  ├── blocked_users
  ├── reports
  └── analytics_events
```

---

# Reglas de matching relacionadas con datos

Un usuario solo puede aparecer como candidato si:

* Su cuenta está activa.
* Su perfil está completo.
* Su perfil es visible.
* Comparte al menos un gimnasio con el usuario actual.
* Tiene al menos una franja compatible.
* No existe bloqueo en ninguna dirección.
* No existe un match activo entre ambos.
* No es el propio usuario.
* No existe una solicitud pendiente equivalente.
* No ha sido rechazado recientemente según la política del producto.

---

# Política de rechazo reciente

Para el MVP, una solicitud rechazada puede excluir al candidato durante:

```text
30 días
```

Este valor debe estar centralizado como configuración.

No debe codificarse en múltiples lugares.

---

# Política de expiración

Las solicitudes pendientes pueden expirar tras:

```text
14 días
```

Este valor debe ser configurable.

---

# Datos de contacto

Los siguientes campos se consideran sensibles:

```text
profiles.contact_type
profiles.contact_value
```

Nunca deben aparecer en:

* Listados de candidatos.
* Resultados de búsqueda.
* Respuestas públicas de perfil.
* Solicitudes pendientes.
* Logs.
* Eventos analíticos.

Solo pueden devolverse si:

* Existe un match.
* El match está activo.
* El usuario autenticado pertenece al match.
* No existe bloqueo entre los usuarios.

---

# Reglas de eliminación

## Eliminación de usuario

Cuando un usuario solicita eliminar su cuenta:

* Cambiar `users.status` a `DELETED`.
* Establecer `deleted_at`.
* Ocultar el perfil.
* Invalidar sesiones.
* Excluirlo del matching.
* Archivar o cancelar solicitudes pendientes.
* Ocultar sus datos de contacto.
* Mantener datos mínimos necesarios para integridad y auditoría.
* Anonimizar cuando sea posible.

## Eliminación de gimnasio

No eliminar físicamente un gimnasio con relaciones.

Preferir marcarlo como inactivo en una futura versión.

---

# Seeds de desarrollo

Crear datos de prueba para:

## Gimnasios

Al menos:

* 3 gimnasios de Huelva.
* 1 gimnasio genérico añadido por usuario.
* 1 gimnasio verificado.
* 1 gimnasio no verificado.

## Usuarios

Al menos:

* 8 usuarios.
* Diferentes niveles.
* Diferentes objetivos.
* Horarios compatibles e incompatibles.
* Un usuario administrador.
* Un usuario suspendido.
* Un perfil incompleto.
* Dos usuarios bloqueados entre sí.

## Solicitudes

Incluir:

* Una solicitud pendiente.
* Una aceptada.
* Una rechazada.
* Una cancelada.

## Matches

Incluir:

* Un match activo.
* Un match con entrenamiento confirmado.
* Un match archivado.

---

# Migraciones

Lovable debe:

1. Crear el esquema de Prisma.
2. Generar una migración inicial.
3. Añadir enums.
4. Añadir claves foráneas.
5. Añadir restricciones únicas.
6. Añadir índices.
7. Añadir checks mediante SQL cuando Prisma no los soporte directamente.
8. Crear un seed reproducible.
9. No modificar migraciones ya ejecutadas.
10. Crear nuevas migraciones para cambios posteriores.

---

# Reglas para Prisma

El archivo `schema.prisma` debe:

* Usar PostgreSQL.
* Usar UUID.
* Incluir enums.
* Incluir relaciones explícitas.
* Incluir nombres de relación cuando haya más de una relación entre las mismas tablas.
* Usar `@unique` cuando corresponda.
* Usar `@@unique` para restricciones compuestas.
* Usar `@@index` para búsquedas frecuentes.
* Mapear nombres de columnas si se usa `snake_case`.
* No almacenar secretos en el esquema.
* Leer `DATABASE_URL` desde variables de entorno.

---

# Resultado esperado

Lovable debe poder generar a partir de este documento:

* `prisma/schema.prisma`
* migración inicial;
* seed de desarrollo;
* tipos TypeScript;
* repositorios;
* validadores;
* servicios de acceso a datos;
* documentación básica de relaciones.

No debe añadir tablas nuevas sin justificar su necesidad y actualizar este documento.
