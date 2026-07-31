# GymBuddy — Plan del MVP con Lovable y PostgreSQL

## 1. Objetivo

Construir en unas 3 horas una web responsive que permita:

1. Registrarse.
2. Crear un perfil de entrenamiento.
3. Seleccionar gimnasio y disponibilidad.
4. Encontrar personas compatibles.
5. Enviar una solicitud.
6. Aceptar o rechazar la solicitud.
7. Compartir el contacto únicamente cuando exista aceptación.
8. Confirmar posteriormente si entrenaron juntos.

La hipótesis que queremos validar es:

> ¿Hay personas dispuestas a quedar con desconocidos compatibles para entrenar en el mismo gimnasio?

---

# 2. Stack obligatorio

Usar:

* React.
* TypeScript.
* Tailwind CSS.
* PostgreSQL.
* API backend propia.
* Prisma como ORM, salvo que el repositorio ya utilice Drizzle.
* Migraciones versionadas.
* Autenticación mediante sesiones seguras.
* Variables de entorno.

No utilizar:

* Supabase.
* Firebase.
* Supabase Auth.
* Supabase Storage.
* SDK de Supabase.
* Conexión directa desde el frontend a PostgreSQL.

El frontend debe comunicarse con una API backend. Solo el backend puede acceder a PostgreSQL.

---

# 3. Archivos que debes crear en el repositorio

```text
README.md
LOVABLE.md
docs/
  product.md
  data-model.md
  analytics.md
```

`LOVABLE.md` debe ser la fuente principal de verdad para Lovable.

---

# 4. Prompt inicial para Lovable

Copia este prompt:

```text
Quiero construir un MVP responsive llamado GymBuddy.

Antes de generar o modificar código, lee completamente:

- LOVABLE.md
- README.md
- docs/product.md
- docs/data-model.md
- docs/analytics.md

Estos documentos son la fuente de verdad del proyecto.

Restricción técnica obligatoria:

- Usa PostgreSQL.
- No uses Supabase.
- No instales @supabase/supabase-js.
- No uses Firebase.
- El frontend no debe conectarse directamente a PostgreSQL.
- Todas las operaciones deben pasar por una API backend.
- Usa Prisma como ORM, salvo que el proyecto ya use Drizzle.
- Crea migraciones versionadas.
- Implementa autenticación mediante sesiones seguras.
- Implementa autorización en el backend.
- Usa variables de entorno para credenciales.

Antes de crear pantallas:

1. Revisa la arquitectura existente.
2. Define el modelo de datos.
3. Crea las migraciones.
4. Añade índices y restricciones.
5. Implementa la conexión con PostgreSQL.
6. Implementa autenticación.
7. Define los endpoints de la API.
8. Implementa después el flujo principal.

No añadas funcionalidades fuera del MVP.
```

---

# 5. Funcionalidades del MVP

## Landing

Debe incluir:

* Título: “Encuentra a alguien con quien entrenar”.
* Explicación breve.
* Botón “Encontrar compañero”.
* Sección “Cómo funciona”.
* Casos de uso.
* Recomendaciones básicas de seguridad.

## Registro e inicio de sesión

Implementar:

* Registro con email y contraseña.
* Inicio de sesión.
* Cierre de sesión.
* Sesión persistente.
* Protección de rutas.
* Contraseñas almacenadas mediante hash seguro.
* Cookies `httpOnly`.
* Roles `user` y `admin`.

No es necesario implementar recuperación de contraseña en esta primera versión.

## Perfil

Campos mínimos:

* Nombre.
* Edad o fecha de nacimiento.
* Ciudad.
* Gimnasio.
* Días de entrenamiento.
* Franja horaria.
* Nivel.
* Objetivo.
* Motivo por el que busca compañero.
* Preferencia de entrenamiento.
* Método de contacto.
* Valor del contacto.

El contacto debe permanecer oculto hasta que exista un match aceptado.

## Niveles

* Principiante.
* Intermedio.
* Avanzado.

## Objetivos

* Ganar masa muscular.
* Perder grasa.
* Mejorar fuerza.
* Powerlifting.
* Fitness general.
* Cross training.
* Retomar el entrenamiento.
* Otro.

## Motivos para buscar compañero

* Soy nuevo en el gimnasio.
* Me da vergüenza entrenar solo.
* Necesito motivación.
* Necesito un spotter.
* Estoy de viaje.
* Me he mudado.
* Mis amigos no entrenan.
* No coincido con mis amigos.
* Otro.

## Preferencia de entrenamiento

* Siempre acompañado.
* Algunas veces acompañado.
* Solo para determinados entrenamientos.
* Normalmente prefiero entrenar solo.

## Gimnasios

Permitir:

* Buscar un gimnasio existente.
* Seleccionar un gimnasio.
* Añadir manualmente un gimnasio si no existe.

Cada gimnasio tendrá:

* Nombre.
* Ciudad.
* Dirección opcional.
* Estado de verificación.

No integrar Google Maps en el MVP.

---

# 6. Sistema de matching

No utilizar inteligencia artificial.

Requisitos obligatorios:

* Mismo gimnasio.
* Al menos un día compatible.
* Horarios solapados.
* Nivel igual o cercano.
* Objetivos compatibles.
* Excluir usuarios bloqueados.
* Excluir al propio usuario.
* Excluir perfiles desactivados.
* Excluir solicitudes ya rechazadas recientemente.

Puntuación inicial:

* Horario compatible: 35 %.
* Días compatibles: 25 %.
* Objetivo: 20 %.
* Nivel: 10 %.
* Preferencia de entrenamiento: 10 %.

El mismo gimnasio es un requisito obligatorio, no parte de la puntuación.

Cada resultado debe mostrar:

* Nombre.
* Edad aproximada.
* Gimnasio.
* Nivel.
* Objetivo.
* Días compatibles.
* Horarios compatibles.
* Motivo para buscar compañero.
* Porcentaje de compatibilidad.

Acciones:

* “Me interesa”.
* “Ahora no”.
* “Ver perfil”.

---

# 7. Solicitudes y matches

Estados de solicitud:

* `pending`
* `accepted`
* `rejected`
* `cancelled`

Flujo:

1. Un usuario envía una solicitud.
2. El receptor la acepta o rechaza.
3. Si la acepta, se crea un match.
4. Solo entonces se muestra la información de contacto.
5. Ambos usuarios pueden bloquear o reportar al otro.

No desarrollar un chat interno.

---

# 8. Seguimiento del entrenamiento

Después del match, permitir responder:

## Pregunta 1

“¿Llegasteis a entrenar juntos?”

* Sí.
* No.
* Todavía no.

## Pregunta 2

“¿Volverías a entrenar con esta persona?”

* Sí.
* No.
* Quizás.

También guardar:

* Valoración del 1 al 5.
* Comentario opcional.

La métrica principal del MVP será el número de entrenamientos reales confirmados.

---

# 9. Modelo de datos mínimo

## users

* id
* email
* password_hash
* role
* status
* created_at
* updated_at

## profiles

* id
* user_id
* display_name
* birth_date
* city
* bio
* photo_url
* experience_level
* primary_goal
* buddy_reason
* training_preference
* contact_type
* contact_value
* profile_completed
* created_at
* updated_at

## gyms

* id
* name
* city
* address
* is_verified
* created_by
* created_at
* updated_at

## user_gyms

* id
* user_id
* gym_id
* is_primary
* created_at

## availability

* id
* user_id
* day_of_week
* start_time
* end_time
* created_at

## strength_marks

* id
* user_id
* exercise
* weight
* repetitions
* estimated_one_rep_max
* created_at
* updated_at

Las marcas son opcionales en el MVP. No deben impedir completar el perfil.

## match_requests

* id
* sender_id
* receiver_id
* gym_id
* compatibility_score
* status
* created_at
* responded_at

## matches

* id
* user_one_id
* user_two_id
* gym_id
* match_request_id
* contact_revealed_at
* planned_workout_at
* status
* created_at

## workout_feedback

* id
* match_id
* user_id
* workout_happened
* would_repeat
* rating
* comment
* created_at

## blocked_users

* id
* blocker_id
* blocked_id
* created_at

## reports

* id
* reporter_id
* reported_user_id
* reason
* description
* status
* created_at

## analytics_events

* id
* user_id
* anonymous_id
* event_name
* event_properties
* created_at

---

# 10. Restricciones e índices

Añadir:

* Email único.
* Un único perfil por usuario.
* Claves foráneas.
* Índice por gimnasio.
* Índice por usuario.
* Índice por estado de solicitud.
* Índice por nivel.
* Índice por objetivo.
* Índice de disponibilidad.
* Restricción para impedir solicitudes duplicadas.
* Restricción para impedir matches duplicados.
* Restricción para impedir que un usuario se envíe una solicitud a sí mismo.

---

# 11. Eventos de analítica

Registrar:

* `landing_viewed`
* `signup_started`
* `signup_completed`
* `profile_started`
* `profile_completed`
* `gym_selected`
* `match_search_started`
* `match_results_viewed`
* `match_request_sent`
* `match_request_accepted`
* `match_request_rejected`
* `contact_revealed`
* `workout_planned`
* `workout_confirmed`
* `repeat_intention_yes`
* `feedback_submitted`

El embudo principal será:

```text
Visitas
→ registros
→ perfiles completados
→ búsquedas
→ solicitudes
→ matches
→ contactos revelados
→ entrenamientos confirmados
→ usuarios que repetirían
```

---

# 12. Pasos de implementación en Lovable

No pedir todo de una vez. Ejecutar estos prompts por orden.

## Paso 1 — Base de datos

```text
Lee LOVABLE.md y todos los archivos de docs.

Configura PostgreSQL con Prisma.

Antes de crear código, presenta brevemente las tablas y relaciones.

Después:

1. Crea el schema de Prisma.
2. Crea las migraciones.
3. Añade claves foráneas, índices y restricciones.
4. Crea un seed con varios gimnasios y usuarios de prueba.
5. Añade scripts para ejecutar migraciones y seeds.
6. Genera los tipos necesarios.

No implementes todavía la interfaz.
No uses Supabase.
```

## Paso 2 — Autenticación

```text
Lee LOVABLE.md.

Implementa autenticación sin Supabase:

1. Registro.
2. Inicio de sesión.
3. Cierre de sesión.
4. Contraseñas con hash seguro.
5. Sesiones mediante cookies httpOnly.
6. Protección de rutas.
7. Roles user y admin.
8. Middleware de autorización.

El frontend nunca debe acceder directamente a PostgreSQL.
```

## Paso 3 — Landing y onboarding

```text
Lee LOVABLE.md.

Implementa:

1. Landing responsive.
2. CTA para encontrar compañero.
3. Registro e inicio de sesión.
4. Onboarding por pasos.
5. Creación y edición del perfil.
6. Selección o creación de gimnasio.
7. Disponibilidad semanal.
8. Nivel, objetivo, motivo y preferencia.

Guarda los datos mediante la API backend.
```

## Paso 4 — Matching

```text
Lee LOVABLE.md.

Implementa el sistema de matching sin IA.

Crea una función backend testeable que calcule compatibilidad usando:

- mismo gimnasio obligatorio;
- días compatibles;
- solapamiento horario;
- nivel;
- objetivo;
- preferencia de entrenamiento.

Excluye:

- al propio usuario;
- usuarios bloqueados;
- perfiles desactivados;
- solicitudes ya rechazadas;
- matches existentes.

Muestra los resultados en tarjetas.
```

## Paso 5 — Solicitudes y matches

```text
Lee LOVABLE.md.

Implementa:

1. Enviar solicitud.
2. Ver solicitudes recibidas.
3. Ver solicitudes enviadas.
4. Aceptar.
5. Rechazar.
6. Crear match.
7. Revelar contacto solo cuando la solicitud esté aceptada.
8. Bloquear usuarios.
9. Reportar usuarios.

No desarrolles chat interno.
```

## Paso 6 — Seguimiento y métricas

```text
Lee LOVABLE.md.

Implementa:

1. Confirmación de entrenamiento real.
2. Pregunta sobre intención de repetir.
3. Valoración del 1 al 5.
4. Comentario opcional.
5. Registro de eventos de analítica.
6. Panel administrador mínimo con el embudo del MVP.
```

---

# 13. Plan de tres horas

## 00:00–00:20 — Preparación

* Crear el repositorio.
* Crear `LOVABLE.md`.
* Crear la carpeta `docs`.
* Añadir los documentos funcionales.
* Preparar PostgreSQL.
* Configurar `DATABASE_URL`.
* Conectar Lovable con el repositorio.
* Confirmar que no existen dependencias de Supabase.

## 00:20–00:50 — Base técnica

* Configurar Prisma.
* Crear el modelo de datos.
* Crear migraciones.
* Ejecutar migraciones.
* Crear seed.
* Configurar autenticación.
* Probar registro e inicio de sesión.

## 00:50–01:20 — Landing y perfil

* Crear landing.
* Crear formulario de registro.
* Crear onboarding.
* Guardar perfil.
* Seleccionar gimnasio.
* Guardar disponibilidad.
* Guardar nivel y objetivo.

## 01:20–02:00 — Matching

* Crear consulta de candidatos.
* Crear función de compatibilidad.
* Mostrar resultados.
* Añadir estado sin resultados.
* Permitir enviar solicitud.

## 02:00–02:30 — Match y contacto

* Mostrar solicitudes recibidas.
* Aceptar o rechazar.
* Crear el match.
* Revelar contacto.
* Añadir bloqueo y reporte.

## 02:30–02:45 — Validación

* Crear encuesta de seguimiento.
* Confirmar entrenamiento.
* Preguntar si repetirían.
* Registrar eventos.

## 02:45–03:00 — Pruebas y publicación

* Probar en móvil.
* Crear dos usuarios diferentes.
* Completar ambos perfiles.
* Comprobar que aparecen como compatibles.
* Enviar una solicitud.
* Aceptarla.
* Comprobar que se revela el contacto.
* Confirmar un entrenamiento.
* Publicar.
* Generar el QR.

---

# 14. Fuera de alcance

No implementar todavía:

* Chat interno.
* Aplicaciones móviles nativas.
* Inteligencia artificial.
* Google Maps.
* Geolocalización en tiempo real.
* Notificaciones push.
* Pagos.
* Suscripciones.
* Rutinas.
* Dietas.
* Feed social.
* Rankings.
* Wearables.
* Sistema avanzado de reputación.
* Panel administrador complejo.

---

# 15. Criterio para considerar terminado el MVP

El MVP está listo cuando dos usuarios diferentes pueden:

1. Registrarse.
2. Completar su perfil.
3. Seleccionar el mismo gimnasio.
4. Indicar horarios compatibles.
5. Encontrarse en los resultados.
6. Enviar y aceptar una solicitud.
7. Ver sus datos de contacto.
8. Confirmar posteriormente que entrenaron juntos.
