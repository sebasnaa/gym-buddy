# GymBuddy — Analytics Specification

Version: 0.1
Scope: MVP
Primary objective: Validate real-world workout partner matching

---

# Objetivo

Este documento define la estrategia de analítica de producto de GymBuddy.

Es la fuente de verdad para:

* Embudo de conversión.
* Eventos.
* Propiedades.
* KPIs.
* Métricas de adquisición.
* Métricas de activación.
* Métricas de matching.
* Confirmación de entrenamientos.
* Retención.
* Panel administrativo.
* Privacidad analítica.
* Criterios de validación del MVP.

El objetivo de la analítica no es maximizar métricas superficiales.

El objetivo es responder a esta pregunta:

```text
¿GymBuddy consigue que dos personas compatibles se conozcan y entrenen juntas?
```

---

# Principio principal

La métrica más importante del MVP es:

```text
Entrenamientos reales confirmados
```

No son métricas principales:

* Visitas.
* Impresiones.
* Seguidores.
* Descargas.
* Tiempo en pantalla.
* Número bruto de perfiles.
* Número bruto de solicitudes.

Estas métricas son útiles para diagnosticar el embudo, pero no validan por sí solas el producto.

---

# North Star Metric

La métrica principal será:

```text
Confirmed Workouts
```

Definición:

Número de conexiones en las que al menos uno de los participantes confirma que el entrenamiento tuvo lugar.

Versión estricta recomendada:

```text
Mutually Confirmed Workouts
```

Definición:

Número de conexiones en las que ambos participantes confirman que el entrenamiento tuvo lugar.

Durante el MVP deben mostrarse ambas métricas por separado.

---

# Métricas principales

## Confirmed Workouts

Entrenamientos confirmados por al menos un participante.

## Mutually Confirmed Workouts

Entrenamientos confirmados por ambos participantes.

## Repeat Intentions

Usuarios que indican que volverían a entrenar con la misma persona.

## Active Connections

Conexiones aceptadas que siguen activas.

## Accepted Match Requests

Solicitudes aceptadas.

## Contact Reveals

Conexiones en las que se reveló correctamente el contacto.

---

# Hipótesis de producto

La analítica debe permitir validar las siguientes hipótesis.

## Hipótesis 1

Existen personas interesadas en encontrar un compañero dentro de su gimnasio.

Señales:

* Registros.
* Perfiles completos.
* Búsquedas.
* Solicitudes enviadas.

## Hipótesis 2

El gimnasio y el horario son filtros suficientes para producir compatibilidad útil.

Señales:

* Resultados mostrados.
* Solicitudes enviadas desde esos resultados.
* Tasa de aceptación.
* Entrenamientos confirmados.

## Hipótesis 3

Los usuarios aceptarán compartir contacto después de una aceptación mutua.

Señales:

* Solicitudes aceptadas.
* Contactos revelados.
* Accesos al contacto.
* Planificación de entrenamientos.

## Hipótesis 4

Una parte de las conexiones digitales se convierte en entrenamientos reales.

Señales:

* Entrenamientos planificados.
* Feedback enviado.
* Entrenamientos confirmados.
* Confirmaciones mutuas.

## Hipótesis 5

Algunos usuarios repetirán con la misma persona.

Señales:

* Intención de repetir.
* Segundo entrenamiento confirmado en versiones futuras.
* Conexiones que permanecen activas.

---

# Embudo principal

El embudo completo del MVP será:

```text
Landing Viewed
        ↓
Signup Started
        ↓
Signup Completed
        ↓
Profile Started
        ↓
Profile Completed
        ↓
Match Search Started
        ↓
Match Results Viewed
        ↓
Candidate Profile Viewed
        ↓
Match Request Sent
        ↓
Match Request Accepted
        ↓
Contact Revealed
        ↓
Workout Planned
        ↓
Workout Confirmed
        ↓
Repeat Intention Yes
```

No todos los usuarios deben pasar necesariamente por `Candidate Profile Viewed`.

Una solicitud puede enviarse directamente desde la tarjeta.

---

# Embudos secundarios

## Embudo de registro

```text
landing_viewed
signup_started
signup_completed
```

## Embudo de onboarding

```text
profile_started
onboarding_step_completed
profile_completed
```

## Embudo de búsqueda

```text
match_search_started
match_results_viewed
candidate_profile_viewed
match_request_sent
```

## Embudo de conexión

```text
match_request_sent
match_request_accepted
contact_revealed
```

## Embudo de entrenamiento

```text
contact_revealed
workout_planned
feedback_submitted
workout_confirmed
repeat_intention_yes
```

---

# Definiciones de conversión

## Visit to Signup Conversion

```text
signup_completed / unique_landing_visitors
```

## Signup Start Conversion

```text
signup_started / unique_landing_visitors
```

## Signup Completion Rate

```text
signup_completed / signup_started
```

## Profile Completion Rate

```text
profile_completed / signup_completed
```

## Search Activation Rate

```text
users_with_match_search / users_with_completed_profile
```

## Results Availability Rate

```text
users_with_results / users_with_match_search
```

## Request Creation Rate

```text
users_who_sent_request / users_who_viewed_results
```

## Request Acceptance Rate

```text
accepted_requests / resolved_requests
```

Donde:

```text
resolved_requests = accepted_requests + rejected_requests
```

También debe mostrarse:

```text
accepted_requests / total_sent_requests
```

para observar solicitudes aún pendientes.

## Contact Reveal Rate

```text
contact_reveals / accepted_requests
```

En condiciones normales debería aproximarse al 100 %.

Una diferencia puede indicar errores técnicos.

## Workout Planning Rate

```text
matches_with_planned_workout / active_matches
```

## Confirmed Workout Rate

```text
confirmed_workouts / accepted_requests
```

También:

```text
confirmed_workouts / contact_reveals
```

## Mutual Confirmation Rate

```text
mutually_confirmed_workouts / confirmed_workouts
```

## Repeat Intention Rate

```text
repeat_intention_yes / feedback_with_workout_happened_yes
```

---

# Unidad de medida

Debe distinguirse entre:

* Eventos.
* Usuarios únicos.
* Sesiones.
* Solicitudes.
* Matches.
* Entrenamientos.

Ejemplo:

Un usuario puede enviar cinco solicitudes.

Por tanto:

```text
match_request_sent events = 5
users_who_sent_request = 1
```

Los paneles deben indicar claramente la unidad utilizada.

---

# Identidad analítica

## anonymousId

Se utilizará antes del registro.

Debe ser:

* Aleatorio.
* Opaco.
* No derivado del correo.
* No derivado de la IP.
* No contener datos personales.

Ejemplo:

```text
anon_c03be5b6d7ac4f1c
```

## userId

Se añade desde el backend cuando existe una sesión autenticada.

El cliente no debe elegir arbitrariamente el `userId`.

## sessionId

Identifica una sesión de navegación analítica.

Debe renovarse después de un periodo de inactividad.

Duración inicial recomendada:

```text
30 minutos
```

## Vinculación

Tras completar el registro, los eventos previos del mismo `anonymousId` pueden relacionarse con el nuevo `userId`.

La vinculación debe realizarse sin copiar datos sensibles.

---

# Fuente de los eventos

## Eventos de frontend

Adecuados para medir:

* Vistas.
* Clics.
* Inicio de flujos.
* Aplicación de filtros.
* Apertura de pantallas.
* Abandono de onboarding.

## Eventos de backend

Obligatorios para acciones críticas:

* Registro completado.
* Perfil completado.
* Solicitud creada.
* Solicitud aceptada.
* Contacto revelado.
* Match creado.
* Feedback enviado.
* Entrenamiento confirmado.
* Usuario bloqueado.
* Usuario reportado.

Los eventos críticos no deben depender únicamente del frontend.

El backend es la fuente de verdad.

---

# Convención de nombres

Los eventos deben usar:

```text
snake_case
```

Formato recomendado:

```text
object_action
```

Ejemplos:

```text
landing_viewed
signup_started
profile_completed
match_request_sent
workout_confirmed
```

No usar nombres inconsistentes como:

```text
UserSignedUp
sign-up-done
registerSuccess
```

---

# Estructura de un evento

Cada evento debe seguir una estructura conceptual equivalente a:

```json
{
  "eventName": "match_request_sent",
  "userId": "uuid",
  "anonymousId": null,
  "sessionId": "opaque-session-id",
  "eventProperties": {
    "gymId": "uuid",
    "compatibilityBand": "HIGH"
  },
  "createdAt": "2026-07-31T15:00:00.000Z"
}
```

El servidor debe establecer:

* `userId`, si hay sesión.
* `createdAt`.
* Propiedades internas de confianza.
* Identificadores derivados de la acción.

---

# Propiedades comunes

Cuando proceda, los eventos pueden incluir:

```text
source
campaign
medium
content
gymId
city
profileCompleted
deviceType
viewportCategory
referrerCategory
route
experimentId
variantId
```

No todos los eventos necesitan todas las propiedades.

---

# Datos prohibidos en analítica

Nunca registrar:

* Contraseñas.
* Hashes.
* Tokens.
* Cookies.
* Correo completo.
* Teléfono.
* Usuario de Telegram.
* Usuario de Instagram.
* Valor de contacto.
* Texto de mensajes privados.
* Bio completa.
* Descripción completa de reportes.
* Dirección postal de usuarios.
* Fecha de nacimiento completa.
* Información médica.
* Fotografías.
* Cabeceras de autenticación.
* URL con tokens.
* IP como identificador permanente.

---

# Catálogo de eventos

---

# Adquisición

## `landing_viewed`

Se dispara cuando la landing se muestra correctamente.

### Fuente

Frontend.

### Propiedades

```text
source
campaign
medium
content
referrerCategory
deviceType
viewportCategory
```

### Reglas

* Contar una vista por carga real.
* Evitar duplicados por re-render de React.
* No disparar en cada cambio de estado.

---

## `primary_cta_clicked`

Se dispara al pulsar la acción principal de la landing.

### Fuente

Frontend.

### Propiedades

```text
ctaLocation
ctaLabel
source
campaign
```

### Valores de `ctaLocation`

```text
hero
how_it_works
footer
navigation
```

---

## `safety_section_viewed`

Opcional.

Indica que el usuario visualizó la sección de seguridad.

No es una métrica principal.

Debe utilizarse solo si puede medirse sin una implementación compleja.

---

# Registro y autenticación

## `signup_started`

Se dispara cuando el usuario comienza el registro.

### Fuente

Frontend.

### Condición

Primera interacción significativa con el formulario o apertura de `/register`.

Elegir una convención y mantenerla.

### Propiedades

```text
source
campaign
```

---

## `signup_validation_failed`

Se dispara cuando el formulario no supera la validación.

### Fuente

Frontend o backend.

### Propiedades permitidas

```text
fieldCategory
errorCode
```

No incluir el valor enviado.

Ejemplo:

```json
{
  "fieldCategory": "password",
  "errorCode": "WEAK_PASSWORD"
}
```

---

## `signup_completed`

Se dispara después de crear la cuenta correctamente.

### Fuente

Backend.

### Propiedades

```text
source
campaign
```

### Reglas

* Una vez por usuario.
* No depender del evento del frontend.
* No incluir correo.

---

## `login_completed`

Se dispara después de un inicio de sesión correcto.

### Fuente

Backend.

### Propiedades

```text
authenticationMethod
```

Valor inicial:

```text
PASSWORD
```

---

## `login_failed`

Evento opcional de seguridad y producto.

### Fuente

Backend.

### Propiedades

```text
failureCategory
```

Valores:

```text
INVALID_CREDENTIALS
ACCOUNT_SUSPENDED
RATE_LIMITED
```

No incluir correo, contraseña ni detalle técnico.

Puede almacenarse en un sistema de seguridad separado si se considera más adecuado.

---

## `logout_completed`

Se dispara después de invalidar la sesión.

### Fuente

Backend.

---

# Onboarding

## `profile_started`

Se dispara cuando el usuario comienza el onboarding.

### Fuente

Frontend.

### Reglas

* Una vez por usuario o por primera entrada.
* No disparar en cada visita posterior.

---

## `onboarding_step_viewed`

Se dispara al mostrar un paso.

### Fuente

Frontend.

### Propiedades

```text
stepNumber
stepName
```

Valores recomendados:

```text
ABOUT
TRAINING
GYM
AVAILABILITY
CONTACT
REVIEW
```

---

## `onboarding_step_completed`

Se dispara al completar correctamente un paso.

### Fuente

Frontend o backend, según persistencia.

### Propiedades

```text
stepNumber
stepName
```

No incluir los valores personales introducidos.

---

## `onboarding_step_validation_failed`

Se dispara cuando no se puede avanzar.

### Fuente

Frontend.

### Propiedades

```text
stepName
errorCode
fieldCategory
```

---

## `onboarding_abandoned`

No debe dispararse explícitamente desde el navegador al cerrar una pestaña.

Debe calcularse analíticamente.

Definición inicial:

Usuario que inicia el onboarding y no completa el perfil dentro de 24 horas.

También pueden observarse abandonos por último paso completado.

---

## `profile_completed`

Se dispara cuando el perfil pasa de incompleto a completo.

### Fuente

Backend.

### Propiedades permitidas

```text
gymCount
availabilitySlotCount
experienceLevel
primaryGoal
trainingPreference
```

Estos valores son categorías de producto, no datos de contacto.

### Reglas

* Disparar al producirse la transición `false → true`.
* Evitar repetirlo cada vez que se actualice el perfil.

---

## `profile_visibility_changed`

Se dispara cuando el usuario activa o pausa su perfil.

### Fuente

Backend.

### Propiedades

```text
visibilityEnabled
```

---

# Gimnasios

## `gym_search_started`

Se dispara al realizar una búsqueda de gimnasio.

### Fuente

Frontend.

### Propiedades

```text
queryLength
citySelected
```

No registrar la consulta completa si puede contener datos personales o texto libre innecesario.

---

## `gym_selected`

Se dispara al asociar un gimnasio existente al perfil.

### Fuente

Backend.

### Propiedades

```text
gymId
isPrimary
verificationStatus
```

---

## `gym_proposed`

Se dispara al crear un gimnasio no encontrado.

### Fuente

Backend.

### Propiedades

```text
gymId
city
```

No registrar la dirección completa en analítica.

---

## `primary_gym_changed`

Se dispara al cambiar el gimnasio principal.

### Fuente

Backend.

### Propiedades

```text
previousGymId
newGymId
```

---

# Disponibilidad

## `availability_added`

Se dispara al añadir una franja.

### Fuente

Backend.

### Propiedades

```text
dayOfWeek
durationMinutes
totalAvailabilitySlots
```

No es necesario registrar horas exactas en analítica si no son imprescindibles.

Para análisis de oferta y demanda pueden utilizarse franjas agregadas:

```text
MORNING
AFTERNOON
EVENING
NIGHT
```

---

## `availability_removed`

Se dispara al eliminar una franja.

### Fuente

Backend.

### Propiedades

```text
dayOfWeek
totalAvailabilitySlots
```

---

## `availability_updated`

Se dispara al modificar una franja.

### Fuente

Backend.

### Propiedades

```text
dayOfWeek
previousDurationMinutes
newDurationMinutes
```

---

# Descubrimiento y matching

## `match_search_started`

Se dispara cuando el usuario solicita candidatos.

### Fuente

Frontend o backend.

Preferencia:

Backend, al procesar una búsqueda válida.

### Propiedades

```text
gymId
dayOfWeekFilterApplied
experienceFilterApplied
goalFilterApplied
minimumScoreBand
```

No almacenar filtros como texto libre.

---

## `match_results_viewed`

Se dispara cuando los resultados se muestran correctamente.

### Fuente

Frontend.

### Propiedades

```text
gymId
resultCount
resultCountBand
filtersAppliedCount
```

Valores de `resultCountBand`:

```text
ZERO
ONE_TO_THREE
FOUR_TO_TEN
MORE_THAN_TEN
```

---

## `no_match_results`

Se dispara cuando la búsqueda válida devuelve cero candidatos.

### Fuente

Backend.

### Propiedades

```text
gymId
reasonCategory
```

Valores posibles:

```text
NO_USERS_IN_GYM
NO_SCHEDULE_OVERLAP
FILTERS_TOO_RESTRICTIVE
ALL_USERS_EXCLUDED
UNKNOWN
```

La categoría puede calcularse cuando sea razonable.

No complicar el MVP si requiere consultas costosas.

---

## `match_filters_opened`

Se dispara al abrir los filtros.

### Fuente

Frontend.

---

## `match_filters_applied`

Se dispara al aplicar filtros.

### Fuente

Frontend.

### Propiedades

```text
filterCount
hasGymFilter
hasDayFilter
hasExperienceFilter
hasGoalFilter
hasMinimumScoreFilter
```

---

## `match_filters_cleared`

Se dispara al limpiar filtros.

### Fuente

Frontend.

---

## `candidate_card_viewed`

Evento opcional.

Solo usar si existe una implementación fiable de visibilidad.

### Fuente

Frontend.

### Propiedades

```text
candidateUserId
position
compatibilityBand
gymId
```

No debe generar un volumen excesivo innecesario.

Puede excluirse del MVP inicial.

---

## `candidate_profile_viewed`

Se dispara al abrir el detalle de un candidato.

### Fuente

Frontend.

### Propiedades

```text
candidateUserId
gymId
compatibilityBand
entryPoint
```

Valores de `entryPoint`:

```text
RESULT_CARD
REQUEST
CONNECTION
OTHER
```

---

## `match_request_started`

Evento opcional.

Se dispara al abrir el formulario de solicitud.

### Fuente

Frontend.

### Propiedades

```text
receiverId
gymId
compatibilityBand
```

---

## `match_request_sent`

Se dispara cuando la solicitud se crea correctamente.

### Fuente

Backend.

### Propiedades

```text
requestId
receiverId
gymId
compatibilityBand
hasMessage
```

No incluir el texto del mensaje.

### Reglas

* Un evento por solicitud creada.
* No disparar en conflictos o duplicados.

---

## `match_request_failed`

Se dispara cuando una solicitud válida desde interfaz no puede crearse.

### Fuente

Backend.

### Propiedades

```text
failureCode
gymId
```

Valores posibles:

```text
DUPLICATE
BLOCKED
NO_SHARED_AVAILABILITY
PROFILE_UNAVAILABLE
RATE_LIMITED
CONFLICT
```

---

## `match_request_viewed`

Se dispara cuando el receptor abre o visualiza la solicitud.

### Fuente

Frontend.

### Propiedades

```text
requestId
ageHours
compatibilityBand
```

Puede ayudar a distinguir falta de interés de falta de exposición.

---

## `match_request_accepted`

Se dispara al aceptar correctamente.

### Fuente

Backend.

### Propiedades

```text
requestId
matchId
gymId
compatibilityBand
responseTimeHours
```

### Reglas

Debe formar parte de la misma transacción lógica que crea el match.

---

## `match_request_rejected`

Se dispara al rechazar.

### Fuente

Backend.

### Propiedades

```text
requestId
gymId
responseTimeHours
```

No solicitar ni registrar un motivo durante el MVP.

---

## `match_request_cancelled`

Se dispara cuando el emisor cancela la solicitud.

### Fuente

Backend.

### Propiedades

```text
requestId
ageHours
```

---

## `match_request_expired`

Se dispara cuando una tarea o proceso marca la solicitud como expirada.

### Fuente

Backend.

### Propiedades

```text
requestId
ageDays
```

Si no existe un proceso de expiración automático en el MVP, puede calcularse en consultas.

---

# Conexiones

## `match_created`

Se dispara cuando se crea una conexión.

### Fuente

Backend.

### Propiedades

```text
matchId
requestId
gymId
compatibilityBand
```

Puede coincidir temporalmente con `match_request_accepted`, pero representa una entidad distinta.

---

## `contact_revealed`

Se dispara cuando el backend autoriza por primera vez el acceso al contacto.

### Fuente

Backend.

### Propiedades

```text
matchId
contactType
```

No incluir `contactValue`.

### Regla

Debe dispararse una sola vez por match si representa la revelación inicial.

---

## `contact_action_clicked`

Se dispara al pulsar “Abrir WhatsApp”, “Abrir Telegram” o equivalente.

### Fuente

Frontend.

### Propiedades

```text
matchId
contactType
```

No confirma que el usuario haya enviado un mensaje.

---

## `connection_viewed`

Se dispara al abrir el detalle de una conexión.

### Fuente

Frontend.

### Propiedades

```text
matchId
matchAgeDays
hasPlannedWorkout
feedbackPending
```

---

## `connection_archived`

Se dispara al archivar.

### Fuente

Backend.

### Propiedades

```text
matchId
matchAgeDays
```

---

# Entrenamiento

## `workout_planning_started`

Se dispara al abrir el formulario de planificación.

### Fuente

Frontend.

### Propiedades

```text
matchId
```

---

## `workout_planned`

Se dispara al guardar una fecha.

### Fuente

Backend.

### Propiedades

```text
matchId
gymId
daysUntilWorkout
```

No es necesario guardar la fecha exacta como propiedad analítica porque ya existe en la entidad de negocio.

---

## `workout_rescheduled`

Se dispara al modificar la fecha.

### Fuente

Backend.

### Propiedades

```text
matchId
previousDaysUntilWorkout
newDaysUntilWorkout
```

---

## `feedback_prompt_viewed`

Se dispara al mostrar la pregunta posterior al entrenamiento.

### Fuente

Frontend.

### Propiedades

```text
matchId
daysSincePlannedWorkout
```

---

## `feedback_started`

Se dispara al responder la primera pregunta.

### Fuente

Frontend.

### Propiedades

```text
matchId
workoutHappened
```

---

## `feedback_submitted`

Se dispara al guardar el feedback.

### Fuente

Backend.

### Propiedades

```text
matchId
workoutHappened
wouldRepeat
ratingProvided
hasComment
```

No incluir el comentario.

---

## `workout_confirmed`

Se dispara cuando un usuario responde:

```text
workoutHappened = YES
```

### Fuente

Backend.

### Propiedades

```text
matchId
gymId
confirmationNumber
daysFromMatchToWorkout
```

`confirmationNumber` puede ser:

```text
FIRST
SECOND
```

Permite distinguir confirmación unilateral y mutua.

---

## `workout_not_completed`

Se dispara cuando un usuario indica que no entrenaron.

### Fuente

Backend.

### Propiedades

```text
matchId
hadPlannedWorkout
daysSinceMatch
```

---

## `workout_mutually_confirmed`

Se dispara cuando el segundo participante confirma el mismo match.

### Fuente

Backend.

### Propiedades

```text
matchId
gymId
daysFromMatchToWorkout
```

### Reglas

* Una vez por match.
* Debe detectarse tras guardar el segundo feedback positivo.
* No depende del frontend.

---

## `repeat_intention_yes`

Se dispara cuando:

```text
wouldRepeat = YES
```

### Fuente

Backend.

### Propiedades

```text
matchId
ratingProvided
```

---

## `repeat_intention_maybe`

Se dispara cuando:

```text
wouldRepeat = MAYBE
```

### Fuente

Backend.

---

## `repeat_intention_no`

Se dispara cuando:

```text
wouldRepeat = NO
```

### Fuente

Backend.

---

# Seguridad y moderación

## `user_blocked`

Se dispara al crear un bloqueo.

### Fuente

Backend.

### Propiedades

```text
blockedUserId
relationshipState
```

Valores de `relationshipState`:

```text
NO_RELATIONSHIP
PENDING_REQUEST
ACTIVE_MATCH
ARCHIVED_MATCH
```

No incluir el motivo textual.

---

## `user_unblocked`

Se dispara al eliminar un bloqueo.

### Fuente

Backend.

### Propiedades

```text
blockedUserId
```

---

## `user_reported`

Se dispara al crear un reporte.

### Fuente

Backend.

### Propiedades

```text
reportedUserId
reason
relationshipState
```

No incluir la descripción.

---

## `report_resolved`

Se dispara cuando un administrador resuelve un reporte.

### Fuente

Backend.

### Propiedades

```text
reportId
resolution
reviewTimeHours
```

---

## `user_suspended`

Se dispara cuando un administrador suspende una cuenta.

### Fuente

Backend.

### Propiedades

```text
userId
reasonCategory
```

Evitar texto libre.

---

# Errores de producto

## `api_error_presented`

Evento opcional.

Se dispara cuando el frontend presenta un error operativo relevante.

### Fuente

Frontend.

### Propiedades

```text
route
errorCode
operation
```

No incluir:

* Mensajes crudos.
* Stack trace.
* Payload.
* Valores de formulario.

---

## `unexpected_client_error`

Debe enviarse preferiblemente a un sistema de observabilidad separado.

No debe mezclarse necesariamente con analítica de producto.

Propiedades:

```text
route
component
errorFingerprint
releaseVersion
```

No incluir estado sensible de React.

---

# Bandas de compatibilidad

Para analítica, la puntuación puede agruparse.

## LOW

```text
0–49
```

## MEDIUM

```text
50–69
```

## HIGH

```text
70–84
```

## VERY_HIGH

```text
85–100
```

Estas bandas son iniciales y deben poder modificarse.

La interfaz no tiene que mostrar necesariamente la puntuación numérica.

---

# Métricas de adquisición

## Unique Landing Visitors

Visitantes anónimos únicos.

## Landing Views

Número total de vistas.

## CTA Click-Through Rate

```text
primary_cta_clicked / landing_viewed
```

## Registrations by Source

Registros agrupados por:

```text
source
campaign
medium
```

Fuentes iniciales esperadas:

```text
qr_gym
instagram
facebook_group
telegram_group
direct
referral
unknown
```

## Cost per Registration

Si existe inversión publicitaria:

```text
advertising_cost / signup_completed
```

## Cost per Completed Profile

```text
advertising_cost / profile_completed
```

## Cost per Confirmed Workout

Métrica especialmente importante:

```text
advertising_cost / confirmed_workouts
```

---

# Métricas de activación

Un usuario se considera activado cuando completa estas acciones:

1. Completa el perfil.
2. Selecciona gimnasio.
3. Añade disponibilidad.
4. Visualiza candidatos o realiza una búsqueda válida.

Definición operativa inicial:

```text
Activated User =
profile_completed
AND match_search_started
```

## Activation Rate

```text
activated_users / signup_completed
```

## Time to Complete Profile

Tiempo entre:

```text
signup_completed
```

y:

```text
profile_completed
```

Mostrar:

* Mediana.
* Percentil 75.
* Distribución por rangos.

## Time to First Search

Tiempo entre registro y primera búsqueda.

## Time to First Result

Tiempo entre registro y primera visualización de candidatos.

---

# Métricas de liquidez

GymBuddy es un producto de matching local.

La liquidez mide si existe suficiente oferta compatible.

## Search Success Rate

```text
searches_with_at_least_one_result / total_valid_searches
```

## Zero Result Rate

```text
searches_with_zero_results / total_valid_searches
```

## Candidates per Search

Mostrar:

* Media.
* Mediana.
* Percentil 25.
* Percentil 75.

## Eligible Users per Gym

Número de perfiles:

* Activos.
* Completos.
* Visibles.
* Con disponibilidad.

Agrupados por gimnasio.

## Schedule Overlap Density

Número medio de candidatos con horario compatible por usuario.

Puede calcularse de forma agregada.

## Gym Liquidity Status

Clasificación inicial:

### EMPTY

```text
0 candidatos potenciales
```

### LOW

```text
1–2 candidatos potenciales
```

### MEDIUM

```text
3–5 candidatos potenciales
```

### HEALTHY

```text
6 o más candidatos potenciales
```

Estos umbrales deben revisarse con datos reales.

---

# Métricas de solicitudes

## Requests per Activated User

```text
match_requests_sent / activated_users
```

## Users Sending at Least One Request

Porcentaje de usuarios activados que envían una solicitud.

## Median Time to First Request

Tiempo entre perfil completado y primera solicitud.

## Requests per Candidate View

```text
match_request_sent / candidate_profile_viewed
```

También puede calcularse desde resultados:

```text
match_request_sent / users_with_match_results
```

## Pending Request Rate

```text
pending_requests / total_sent_requests
```

## Request Response Rate

```text
resolved_requests / total_requests_received
```

## Median Response Time

Tiempo entre envío y aceptación o rechazo.

## Cross-Gym Comparison

Comparar por gimnasio:

* Solicitudes enviadas.
* Tasa de aceptación.
* Tiempo de respuesta.
* Entrenamientos confirmados.

---

# Métricas de conexión

## Matches per 100 Registrations

```text
accepted_requests / signup_completed * 100
```

## Matches per Completed Profile

```text
accepted_requests / profile_completed
```

## Contact Access Success Rate

```text
successful_contact_reveals / accepted_requests
```

Debe estar cerca del 100 %.

## Contact Action Rate

```text
contact_action_clicked / contact_revealed
```

No debe interpretarse como conversación confirmada.

## Time to First Match

Tiempo entre registro y primera solicitud aceptada.

Mostrar mediana y distribución.

---

# Métricas de entrenamiento

## Planned Workout Rate

```text
matches_with_planned_workout / active_matches
```

## Feedback Submission Rate

```text
matches_with_any_feedback / eligible_matches_for_feedback
```

## Workout Confirmation Rate

```text
matches_with_any_yes_feedback / matches_with_any_feedback
```

También:

```text
matches_with_any_yes_feedback / accepted_requests
```

## Mutual Workout Confirmation Rate

```text
matches_with_two_yes_feedbacks / matches_with_any_yes_feedback
```

## No-Show or Not-Completed Rate

```text
matches_with_no_feedback_response / matches_with_feedback
```

Debe interpretarse con cautela.

Un `NO` no significa necesariamente comportamiento negativo.

## Median Time from Match to Workout

Tiempo entre:

```text
match_created
```

y el entrenamiento planificado o confirmado.

## Repeat Intention Rate

```text
would_repeat_yes / feedback_where_workout_happened_yes
```

## Negative Repeat Rate

```text
would_repeat_no / feedback_where_workout_happened_yes
```

---

# Métricas de retención

El MVP tendrá poco volumen, por lo que la retención debe interpretarse con cuidado.

## Weekly Active User

Usuario que realiza al menos una acción significativa durante una semana.

Acciones significativas:

```text
match_search_started
candidate_profile_viewed
match_request_sent
match_request_accepted
connection_viewed
workout_planned
feedback_submitted
```

No contar únicamente:

```text
login_completed
landing_viewed
```

## Week 1 Retention

```text
users_active_in_week_1_after_signup / users_registered_in_cohort
```

## Week 4 Retention

```text
users_active_in_week_4_after_signup / users_registered_in_cohort
```

## Repeat Search Rate

Usuarios que realizan búsquedas en al menos dos días distintos.

## Repeat Connection Engagement

Usuarios que vuelven a abrir una conexión en días distintos.

## Profile Reactivation Rate

Usuarios que pausaron el perfil y luego lo reactivaron.

---

# Cohortes

Las métricas deben poder analizarse por cohortes.

## Cohorte de registro

Agrupar por:

* Día.
* Semana.
* Mes.

## Fuente de adquisición

Agrupar por:

* QR de gimnasio.
* Instagram.
* Grupo local.
* Referencia.
* Tráfico directo.

## Gimnasio

Especialmente importante para observar liquidez local.

## Ciudad

Inicialmente Huelva.

Preparar para múltiples ciudades sin asumirlas en todas las consultas.

## Nivel

```text
BEGINNER
INTERMEDIATE
ADVANCED
```

## Objetivo

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

## Preferencia

```text
ALWAYS_WITH_PARTNER
SOMETIMES_WITH_PARTNER
ONLY_FOR_SPECIFIC_WORKOUTS
USUALLY_ALONE
```

No crear cohortes tan pequeñas que puedan identificar a una persona.

---

# Segmentos prioritarios

Durante la validación deben compararse especialmente:

## Principiantes

Hipótesis:

Pueden valorar seguridad, motivación y acompañamiento.

## Usuarios que necesitan spotter

Hipótesis:

Tienen una necesidad concreta y urgente.

## Personas recién mudadas

Hipótesis:

Tienen una necesidad social y práctica clara.

## Personas con horarios incompatibles con amigos

Hipótesis:

La utilidad depende fuertemente del solapamiento horario.

## Viajeros

Hipótesis:

Podrían tener alta intención, pero poca recurrencia.

No asumir que un segmento es mejor sin datos.

---

# Métricas de seguridad

## Block Rate

```text
users_blocked / accepted_matches
```

También medir:

```text
blocks / active_users
```

## Report Rate

```text
reports / accepted_matches
```

## Reports by Reason

Agrupados por categoría.

## Post-Match Block Rate

Bloqueos producidos después de una conexión.

## Report Resolution Time

Tiempo desde creación hasta resolución.

## Suspended Users

Número y porcentaje de usuarios suspendidos.

Las métricas de seguridad no deben ocultarse para mejorar artificialmente las cifras de crecimiento.

---

# Métricas de calidad técnica

## API Error Rate

```text
failed_api_requests / total_api_requests
```

## Critical Flow Error Rate

Errores en:

* Registro.
* Perfil.
* Búsqueda.
* Solicitud.
* Aceptación.
* Contacto.
* Feedback.

## Contact Reveal Failure Rate

Debe ser prácticamente cero.

## Duplicate Prevention Conflicts

Número de intentos de:

* Solicitud duplicada.
* Aceptación doble.
* Match duplicado.
* Feedback duplicado.

Pueden revelar problemas de interfaz o concurrencia.

## Median API Latency

Medir por endpoint.

## Page Load Performance

Observar métricas web cuando resulte posible:

* LCP.
* INP.
* CLS.

No convertir la optimización técnica en prioridad por encima de validar el producto.

---

# Dashboard del MVP

El panel principal debe mostrar un periodo seleccionable.

Filtros mínimos:

```text
from
to
gymId
city
source
```

---

# Sección 1 — Resumen

Mostrar tarjetas con:

* Visitantes únicos.
* Registros.
* Perfiles completos.
* Usuarios activados.
* Solicitudes enviadas.
* Solicitudes aceptadas.
* Contactos revelados.
* Entrenamientos confirmados.
* Entrenamientos confirmados mutuamente.
* Intenciones de repetir.

---

# Sección 2 — Embudo

Mostrar:

```text
Visits
Signups
Completed Profiles
Activated Users
Requests Sent
Accepted Requests
Confirmed Workouts
Repeat Intentions
```

Para cada etapa:

* Total.
* Conversión desde etapa anterior.
* Conversión desde la primera etapa.

---

# Sección 3 — Liquidez

Mostrar por gimnasio:

* Perfiles elegibles.
* Usuarios activos.
* Búsquedas.
* Búsquedas sin resultados.
* Candidatos medios por búsqueda.
* Solicitudes.
* Matches.
* Entrenamientos.

---

# Sección 4 — Solicitudes

Mostrar:

* Enviadas.
* Pendientes.
* Aceptadas.
* Rechazadas.
* Canceladas.
* Expiradas.
* Tasa de respuesta.
* Tasa de aceptación.
* Mediana de tiempo de respuesta.

---

# Sección 5 — Entrenamientos

Mostrar:

* Entrenamientos planificados.
* Feedback recibido.
* Confirmados por una persona.
* Confirmados por ambas.
* No realizados.
* Intención de repetir.
* Mediana de días desde conexión hasta entrenamiento.

---

# Sección 6 — Seguridad

Mostrar:

* Bloqueos.
* Reportes.
* Reportes abiertos.
* Reportes resueltos.
* Usuarios suspendidos.
* Tasa de reportes por conexión.

No mostrar detalles personales en el dashboard agregado.

---

# Sección 7 — Adquisición

Mostrar por fuente:

* Visitas.
* Registros.
* Perfiles completos.
* Matches.
* Entrenamientos confirmados.
* Coste, si existe.
* Coste por entrenamiento confirmado.

Esta sección permite evaluar si los QR físicos funcionan mejor que los anuncios.

---

# Informe semanal

Durante la validación inicial debe generarse un resumen semanal.

## Contenido

### Adquisición

* Nuevas visitas.
* Nuevos registros.
* Fuente principal.

### Activación

* Perfiles completos.
* Tiempo de finalización.
* Principales abandonos.

### Liquidez

* Gimnasios con mayor y menor oferta.
* Búsquedas sin resultados.
* Horarios con poca coincidencia.

### Matching

* Solicitudes enviadas.
* Tasa de aceptación.
* Tiempo de respuesta.

### Resultado real

* Entrenamientos confirmados.
* Confirmaciones mutuas.
* Intención de repetir.

### Seguridad

* Bloqueos.
* Reportes.
* Incidentes relevantes.

### Aprendizajes

* Qué funcionó.
* Qué bloquea al usuario.
* Qué hipótesis pierde fuerza.
* Qué cambio probar la semana siguiente.

---

# Criterios de validación inicial

El MVP puede considerarse prometedor si, en una prueba local limitada, alcanza aproximadamente:

```text
100 visitas
25 registros
15 perfiles completos
10 solicitudes enviadas
5 solicitudes aceptadas
3 entrenamientos confirmados
2 usuarios con intención de repetir
```

Estas cifras no son objetivos comerciales.

Son umbrales iniciales para evaluar si existe una señal de demanda.

---

# Interpretación de resultados

## Muchas visitas y pocos registros

Posibles causas:

* Propuesta de valor débil.
* Landing poco clara.
* Falta de confianza.
* Tráfico poco relevante.
* Miedo a compartir datos.

Acciones:

* Entrevistas.
* Mejorar explicación.
* Revisar fuente de tráfico.
* Reforzar privacidad.

## Muchos registros y pocos perfiles completos

Posibles causas:

* Onboarding demasiado largo.
* Campos confusos.
* Falta de gimnasio.
* Contacto percibido como invasivo.
* Errores técnicos.

Acciones:

* Revisar abandono por paso.
* Reducir fricción.
* Mejorar microcopy.
* Permitir guardado parcial.

## Muchos perfiles y pocas búsquedas

Posibles causas:

* Redirección incorrecta.
* Llamada a la acción débil.
* Usuario no entiende qué ocurre después.
* Falta de candidatos anticipada.

## Muchas búsquedas y cero resultados

Posibles causas:

* Falta de liquidez.
* Gimnasio demasiado fragmentado.
* Horarios demasiado estrictos.
* Filtros excesivos.
* Datos de prueba mal configurados.

Esta señal puede indicar un problema de mercado local, no necesariamente de interfaz.

## Muchos resultados y pocas solicitudes

Posibles causas:

* Perfiles poco atractivos o incompletos.
* Compatibilidad poco convincente.
* Falta de confianza.
* Miedo al rechazo.
* Acción demasiado comprometida.
* Usuarios explorando sin intención real.

## Muchas solicitudes y pocas aceptaciones

Posibles causas:

* Usuarios inactivos.
* Solicitudes no visibles.
* Falta de notificaciones.
* Mala calidad del matching.
* Mensajes insuficientes.
* Desequilibrio entre oferta y demanda.

## Muchas aceptaciones y pocos contactos usados

Posibles causas:

* Fallo al mostrar el contacto.
* Tipo de contacto poco conveniente.
* Aceptación impulsiva.
* Falta de orientación posterior.

## Muchos contactos y pocos entrenamientos

Esta es una señal crítica.

Posibles causas:

* Horarios no eran realmente compatibles.
* Falta de compromiso.
* Problemas de seguridad o confianza.
* Usuarios no saben cómo iniciar la conversación.
* No existe suficiente urgencia.
* Fricción fuera de la aplicación.

## Entrenamientos confirmados pero baja repetición

Posibles causas:

* Compatibilidad superficial.
* Diferencias de nivel.
* Diferencias de ritmo.
* Objetivos no alineados.
* La necesidad era puntual.

No necesariamente invalida todos los casos de uso.

Por ejemplo, un spotter puntual puede seguir generando valor.

---

# Decisiones basadas en datos

No deben añadirse funcionalidades únicamente porque varios usuarios las soliciten.

Antes de desarrollar una funcionalidad, comprobar:

1. Qué problema resuelve.
2. En qué etapa del embudo actúa.
3. Qué métrica debería mejorar.
4. Si existe evidencia cuantitativa.
5. Si existe evidencia cualitativa.
6. Si puede probarse de forma más sencilla.

Ejemplo:

Antes de crear chat interno, comprobar si el bajo número de entrenamientos se debe realmente al uso de WhatsApp o Telegram.

---

# Analítica cualitativa

Las métricas no explican por sí solas el comportamiento.

Durante el MVP deben realizarse entrevistas breves.

## Usuarios recomendados

* Registrados que no completaron perfil.
* Perfiles completos que no enviaron solicitud.
* Usuarios cuya solicitud fue rechazada.
* Usuarios con match sin entrenamiento.
* Usuarios que sí entrenaron.
* Usuarios que repetirían.
* Usuarios que bloquearon o reportaron.

## Preguntas después de un entrenamiento

* ¿Cómo organizasteis el entrenamiento?
* ¿Qué te dio confianza?
* ¿Qué te generó dudas?
* ¿Coincidíais realmente en horario y nivel?
* ¿Volverías a usar GymBuddy?
* ¿Volverías a entrenar con esa persona?
* ¿Qué fue innecesario?
* ¿Qué faltó?

No incluir respuestas cualitativas completas en la tabla de eventos.

Deben almacenarse por separado y con acceso limitado.

---

# Tracking de campañas

Los enlaces y QR pueden utilizar parámetros UTM.

Ejemplo conceptual:

```text
utm_source=qr_gym
utm_medium=poster
utm_campaign=huelva_validation
utm_content=gym_centro_entrance
```

## Campos permitidos

```text
utm_source
utm_medium
utm_campaign
utm_content
```

## Reglas

* Normalizar valores.
* Limitar longitud.
* No incluir datos personales.
* Guardar la primera fuente de adquisición.
* Opcionalmente guardar la última fuente.

---

# Identificación de QR

Cada cartel debe utilizar un identificador diferente.

Ejemplos:

```text
gym_centro_reception
gym_centro_locker_room
gym_norte_entrance
```

Esto permite conocer:

* Qué gimnasio genera registros.
* Qué ubicación funciona mejor.
* Qué gimnasios producen entrenamientos reales.

No crear identificadores con nombres de personas.

---

# Consentimiento y privacidad

La analítica debe respetar la normativa aplicable.

## Analítica esencial

Puede incluir eventos estrictamente necesarios para:

* Seguridad.
* Funcionamiento.
* Integridad.
* Prevención de abuso.
* Medición interna mínima.

## Analítica no esencial

Cuando sea necesario, debe requerir consentimiento.

Ejemplos:

* Herramientas de terceros.
* Publicidad.
* Seguimiento entre sitios.
* Cookies no esenciales.

## Requisitos

* Política de privacidad clara.
* Consentimiento cuando corresponda.
* Posibilidad de retirar consentimiento.
* No cargar herramientas no esenciales antes del consentimiento.
* Documentar proveedores y retención.

---

# Proveedores externos

Para el MVP se debe evitar depender de demasiados proveedores.

Opciones posibles:

* Analítica interna en PostgreSQL.
* PostHog autohospedado o cloud, si se aprueba.
* Plausible para analítica web agregada.
* Herramienta equivalente con configuración de privacidad.

La fuente de verdad de eventos críticos seguirá siendo el backend y PostgreSQL.

No enviar a terceros:

* Contactos.
* Mensajes.
* Reportes.
* Correos.
* Fechas de nacimiento.
* Información sensible.

---

# Retención de datos analíticos

Propuesta inicial:

## Eventos de producto detallados

```text
12 meses
```

## Métricas agregadas

Pueden conservarse durante más tiempo.

## Eventos de seguridad

Según política legal y operativa específica.

## Datos asociados a cuentas eliminadas

* Anonimizar cuando sea posible.
* Mantener únicamente lo necesario.
* Evitar reconstruir la identidad.
* Respetar requisitos legales y de auditoría.

Los periodos definitivos deben validarse con asesoramiento legal antes de producción.

---

# Calidad de datos

## Reglas

* Evitar eventos duplicados.
* Utilizar timestamps del servidor en eventos críticos.
* Validar nombres de eventos.
* Validar propiedades.
* Rechazar propiedades desconocidas cuando sea razonable.
* Monitorizar eventos sin usuario ni `anonymousId`.
* Documentar cambios de esquema.
* No renombrar eventos históricos sin migración o mapeo.

## Event ID

Cada evento puede tener un UUID único.

Esto permite deduplicación.

Ejemplo:

```text
event_id UUID UNIQUE
```

## Idempotencia

Los eventos de backend deben evitar duplicarse durante reintentos.

Puede utilizarse una clave relacionada con la entidad.

Ejemplo conceptual:

```text
match_request_sent:{requestId}
```

---

# Versionado de eventos

Los eventos pueden incluir:

```text
schemaVersion
```

Valor inicial:

```text
1
```

Cuando se modifica de forma incompatible la estructura de propiedades, debe incrementarse.

No cambiar silenciosamente el significado de una propiedad.

---

# Backfill

Si una métrica puede calcularse directamente desde tablas de negocio, debe preferirse esa fuente para datos históricos.

Ejemplos:

* Usuarios registrados.
* Perfiles completos.
* Solicitudes.
* Matches.
* Feedback.
* Bloqueos.
* Reportes.

Los eventos son útiles para:

* Vistas.
* Clics.
* Inicio de flujos.
* Abandonos.
* Navegación.

Las entidades de negocio son la fuente de verdad para resultados críticos.

---

# Fuente de verdad por métrica

## Registros

Tabla:

```text
users
```

## Perfiles completos

Tabla:

```text
profiles
```

## Solicitudes

Tabla:

```text
match_requests
```

## Matches

Tabla:

```text
matches
```

## Contactos revelados

Campo:

```text
matches.contact_revealed_at
```

## Entrenamientos planificados

Campo:

```text
matches.planned_workout_at
```

## Feedback

Tabla:

```text
workout_feedback
```

## Entrenamientos confirmados

Derivados de:

```text
workout_feedback.workout_happened = YES
```

## Intención de repetir

Derivada de:

```text
workout_feedback.would_repeat = YES
```

## Visitas y navegación

Tabla:

```text
analytics_events
```

---

# Consultas de referencia

## Registros por día

```sql
SELECT
  DATE_TRUNC('day', created_at) AS day,
  COUNT(*) AS registrations
FROM users
WHERE status <> 'DELETED'
GROUP BY 1
ORDER BY 1;
```

Esta consulta es orientativa.

La implementación debe utilizar Prisma o consultas seguras dentro de la capa de datos.

---

## Perfiles completos

```sql
SELECT COUNT(*)
FROM profiles
WHERE profile_completed = TRUE;
```

---

## Solicitudes aceptadas

```sql
SELECT COUNT(*)
FROM match_requests
WHERE status = 'ACCEPTED';
```

---

## Entrenamientos confirmados

```sql
SELECT COUNT(DISTINCT match_id)
FROM workout_feedback
WHERE workout_happened = 'YES';
```

---

## Entrenamientos confirmados mutuamente

```sql
SELECT COUNT(*)
FROM (
  SELECT match_id
  FROM workout_feedback
  WHERE workout_happened = 'YES'
  GROUP BY match_id
  HAVING COUNT(DISTINCT user_id) = 2
) confirmed_matches;
```

---

## Intención de repetir

```sql
SELECT
  COUNT(*) FILTER (WHERE would_repeat = 'YES')::DECIMAL
  /
  NULLIF(COUNT(*) FILTER (WHERE workout_happened = 'YES'), 0)
FROM workout_feedback;
```

Las consultas definitivas deben revisar:

* Usuarios eliminados.
* Datos de prueba.
* Rango temporal.
* Zona horaria.
* Estados archivados.
* Duplicados.

---

# Exclusión de datos internos

Los entornos de desarrollo, test y staging no deben mezclarse con producción.

Cada evento debe asociarse implícita o explícitamente a un entorno:

```text
development
test
staging
production
```

Los dashboards de negocio deben usar únicamente producción.

Los seeds deben excluirse de las métricas reales.

---

# Usuarios internos

Las cuentas de:

* Desarrollo.
* Administración.
* Pruebas.
* Demostración.

deben poder marcarse como internas.

Campo futuro recomendado:

```text
is_internal
```

o tabla equivalente.

Las cuentas internas deben excluirse de las métricas de validación.

Si no se añade el campo durante el MVP, mantener una lista explícita y segura de IDs internos.

---

# Alertas

No es necesario construir un sistema complejo de alertas, pero deben detectarse situaciones críticas.

## Contact Reveal Failure

Alertar si:

```text
accepted_requests > contact_reveals
```

sin explicación.

## Sudden Registration Failure

Alertar si el registro cae a cero mientras existen visitas.

## High Error Rate

Alertar si las operaciones críticas superan un umbral de error.

## Safety Spike

Alertar si aumentan significativamente:

* Bloqueos.
* Reportes.
* Suspensiones.

## Database Event Failure

Alertar si los eventos críticos no pueden registrarse.

La operación de negocio no debe fallar únicamente porque falle la analítica secundaria.

---

# Fallos de analítica

## Eventos no críticos

Si falla un evento de vista o clic:

* No bloquear al usuario.
* Registrar el fallo técnico cuando sea posible.
* Continuar la operación.

## Eventos críticos

Los resultados críticos deben poder reconstruirse desde las tablas de negocio.

No hacer depender la creación de un match del éxito de una herramienta externa de analítica.

Si se utiliza una tabla interna dentro de la misma transacción, debe valorarse el impacto.

---

# Rendimiento

La analítica no debe degradar la experiencia.

## Reglas

* Enviar eventos de frontend de forma asíncrona.
* Agrupar cuando sea razonable.
* Limitar payload.
* Añadir índices adecuados.
* No realizar agregaciones pesadas en cada vista de usuario.
* Ejecutar informes complejos fuera de flujos críticos.
* Paginar eventos administrativos.
* Evitar almacenar propiedades JSONB innecesarias.

---

# Seguridad de acceso

## Usuario normal

No puede consultar eventos analíticos.

## Administrador

Puede consultar métricas agregadas.

No debe tener acceso indiscriminado a eventos detallados salvo necesidad operativa.

## Acceso técnico

Debe limitarse por rol y entorno.

## Exportaciones

Las exportaciones deben:

* Registrar auditoría.
* Limitar datos personales.
* Tener acceso restringido.
* Evitar hojas o archivos públicos.

---

# Tests mínimos

## Eventos de frontend

* `landing_viewed` no se duplica por re-render.
* `signup_started` se dispara una vez según la convención.
* Los filtros no envían texto sensible.
* Los errores no incluyen valores de campos.

## Eventos de backend

* `signup_completed` se genera una vez.
* `profile_completed` solo se genera en transición a completo.
* `match_request_sent` corresponde a una solicitud real.
* `match_request_accepted` corresponde a una aceptación real.
* `contact_revealed` no incluye el valor de contacto.
* `workout_confirmed` se genera tras feedback positivo.
* `workout_mutually_confirmed` se genera una sola vez.
* `user_reported` no contiene descripción.

## Privacidad

* Ningún evento contiene contraseña.
* Ningún evento contiene contacto.
* Ningún evento contiene correo completo.
* Ningún evento contiene texto de mensaje.
* Ningún evento contiene descripción de reporte.

## Calidad

* Eventos con nombres no permitidos son rechazados.
* Propiedades excesivas son rechazadas o filtradas.
* Reintentos no duplican eventos críticos.
* Datos de test no aparecen en producción.

---

# Criterios de aceptación

La analítica se considera correctamente implementada cuando:

* Existe una lista blanca de eventos.
* Los eventos críticos proceden del backend.
* Los resultados principales pueden reconstruirse desde tablas de negocio.
* Se distinguen eventos de usuarios únicos.
* Se conservan `anonymousId`, `userId` y `sessionId` correctamente.
* No se almacenan datos sensibles.
* Las campañas y QR pueden atribuir registros.
* El dashboard muestra el embudo completo.
* Se puede filtrar por fecha, gimnasio, ciudad y fuente.
* Se calculan entrenamientos confirmados y mutuamente confirmados.
* Se calcula la intención de repetir.
* Se excluyen usuarios internos y datos de prueba.
* Los errores de analítica no bloquean flujos no críticos.
* Los cambios de eventos están versionados.
* Existen tests de privacidad y deduplicación.
* Las métricas están definidas de forma inequívoca.

---

# Orden de implementación

Lovable debe implementar la analítica en este orden:

1. Convenciones y tipos TypeScript.
2. Lista blanca de eventos.
3. Tabla `analytics_events`.
4. Captura de `anonymousId`.
5. Captura de `sessionId`.
6. Vinculación con `userId`.
7. Eventos de landing y registro.
8. Eventos de onboarding.
9. Eventos de búsqueda.
10. Eventos críticos de backend.
11. Eventos de entrenamiento.
12. Métricas derivadas desde tablas de negocio.
13. Dashboard administrativo.
14. Filtros por periodo, gimnasio y fuente.
15. Exclusión de cuentas internas.
16. Tests de privacidad.
17. Tests de deduplicación.
18. Informe semanal.

---

# Reglas para Lovable

Lovable debe:

* Tratar los entrenamientos confirmados como resultado principal.
* No utilizar métricas de vanidad como prueba de éxito.
* Registrar las acciones críticas desde el backend.
* No confiar en `userId` enviado por el cliente.
* No incluir datos sensibles en eventos.
* Utilizar nombres de eventos consistentes.
* Mantener una lista blanca de propiedades.
* Evitar duplicados.
* Diferenciar usuarios, eventos, matches y entrenamientos.
* Permitir análisis por gimnasio y fuente.
* Excluir datos internos.
* Mantener alineados código, documentación y dashboard.
* Actualizar este documento antes de añadir eventos nuevos.
* No integrar herramientas externas sin revisar privacidad y seguridad.
* No enviar contactos, mensajes o reportes a proveedores de analítica.
