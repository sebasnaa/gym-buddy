# GymBuddy - Product Specification

Version: 0.1 (MVP)

---

# Visión del producto

GymBuddy es una plataforma que conecta personas que entrenan en el mismo gimnasio para que puedan encontrar compañeros de entrenamiento compatibles según sus horarios, objetivos y nivel.

El objetivo no es crear una red social fitness.

El objetivo es facilitar que dos personas puedan conocerse y entrenar juntas cuando ambas estén interesadas.

---

# Problema

Muchas personas entrenan solas.

Las razones son muy variadas:

* Acaban de empezar en el gimnasio.
* Se han mudado recientemente.
* Están de viaje.
* Sus amigos ya no entrenan.
* Sus horarios no coinciden con los de sus amigos.
* Les da vergüenza entrenar solos.
* Necesitan un spotter para determinados ejercicios.
* Buscan alguien que les motive a ser constantes.

Actualmente no existe una solución sencilla para conectar personas dentro del mismo gimnasio.

La mayoría utiliza:

* WhatsApp
* Instagram
* Grupos de Telegram
* Amigos
* Casualidad

Ninguna de estas opciones está diseñada específicamente para este problema.

---

# Hipótesis

La hipótesis principal del MVP es:

> Existen personas dispuestas a contactar con desconocidos compatibles para entrenar en el mismo gimnasio.

Hipótesis secundarias:

* El horario es más importante que la edad.
* El mismo gimnasio es imprescindible.
* Compartir objetivos mejora la probabilidad de entrenar juntos.
* La mayoría de usuarios no necesita un compañero permanente.
* Muchos usuarios buscan compañía únicamente para algunos entrenamientos.

---

# Objetivos del MVP

El MVP debe validar si existe interés real.

No pretende generar ingresos.

No pretende construir comunidad.

No pretende sustituir las redes sociales.

Únicamente debe responder si existe una necesidad.

---

# Público objetivo

## Perfil 1

Persona nueva en el gimnasio.

Problemas:

* No conoce a nadie.
* Tiene inseguridad.
* Necesita apoyo.

---

## Perfil 2

Persona que siempre entrena sola.

Problemas:

* Falta de motivación.
* Entrenos aburridos.
* No tiene spotter.

---

## Perfil 3

Persona que se muda de ciudad.

Problemas:

* No conoce gente.
* Quiere hacer nuevos contactos.
* Busca mantener la rutina.

---

## Perfil 4

Viajeros frecuentes.

Problemas:

* Cambian de gimnasio.
* Quieren entrenar acompañados.
* No conocen usuarios locales.

---

## Perfil 5

Powerlifters.

Problemas:

* Necesitan spotter.
* Buscan alguien con fuerza similar.

---

# Usuarios que NO son el objetivo

No buscamos inicialmente:

* Influencers fitness.
* Entrenadores personales.
* Gimnasios.
* Deportistas profesionales.
* Personas que únicamente buscan citas.

---

# Propuesta de valor

Entrena con alguien compatible.

Sin grupos.

Sin ruido.

Sin redes sociales.

Simplemente encuentra alguien con quien entrenar.

---

# Casos de uso

## Caso 1

María llega a un gimnasio nuevo.

Completa su perfil.

Encuentra otra chica que también entrena a las 19:00.

Hablan.

Quedan.

Entrenan juntas.

---

## Caso 2

Carlos necesita un spotter.

Busca usuarios avanzados.

Encuentra varios compatibles.

Solicita contacto.

Uno acepta.

---

## Caso 3

Pedro está una semana de vacaciones.

Selecciona un gimnasio temporal.

Encuentra usuarios que entrenan allí.

---

# Flujo principal

Landing

↓

Registro

↓

Crear perfil

↓

Seleccionar gimnasio

↓

Indicar horarios

↓

Buscar compañeros

↓

Enviar solicitud

↓

Aceptar solicitud

↓

Compartir contacto

↓

Entrenar

↓

Confirmar entrenamiento

---

# Matching

La compatibilidad dependerá principalmente de:

* Mismo gimnasio.
* Horarios.
* Días.
* Objetivos.
* Nivel.

No se utilizará inteligencia artificial en el MVP.

El algoritmo será completamente determinista.

---

# Contacto

Los usuarios NO podrán contactar directamente.

Solo podrán hacerlo cuando ambas personas acepten.

Antes de eso:

* No se mostrará teléfono.
* No se mostrará Telegram.
* No se mostrará Instagram.

---

# Confianza

GymBuddy debe transmitir seguridad.

Principios:

* El contacto siempre requiere aceptación mutua.
* El usuario puede bloquear perfiles.
* El usuario puede reportar perfiles.
* Se recomienda quedar siempre dentro del gimnasio.

---

# Qué mediremos

No mediremos únicamente registros.

Las métricas importantes son:

* Usuarios registrados.
* Perfiles completos.
* Solicitudes enviadas.
* Solicitudes aceptadas.
* Matches.
* Contactos compartidos.
* Entrenamientos confirmados.
* Usuarios que repetirían.

---

# Indicadores de éxito

Consideraremos el MVP validado si conseguimos:

* Más de 100 visitas.
* Más de 25 registros.
* Más de 10 solicitudes.
* Al menos 5 matches.
* Al menos 3 entrenamientos confirmados.

---

# Funcionalidades incluidas

* Landing.
* Registro.
* Inicio de sesión.
* Perfil.
* Gimnasios.
* Horarios.
* Matching.
* Solicitudes.
* Matches.
* Compartir contacto.
* Confirmación del entrenamiento.
* Feedback.

---

# Funcionalidades excluidas

No forman parte del MVP:

* Chat.
* IA.
* Pagos.
* Suscripciones.
* Push Notifications.
* Geolocalización.
* Feed social.
* Stories.
* Rutinas.
* Dietas.
* Marketplace.
* Ranking.
* Gamificación.
* Wearables.
* Integración con Apple Health.
* Integración con Google Fit.

---

# Principios del producto

1. Mobile First.
2. Simplicidad.
3. Velocidad.
4. Seguridad.
5. Privacidad.
6. Aceptación mutua antes del contacto.
7. El gimnasio es el centro del producto.
8. Cada pantalla debe tener un único objetivo.
9. Menos funcionalidades, mejor experiencia.
10. Validar antes de construir.

---

# Roadmap

## MVP

* Registro.
* Perfil.
* Matching.
* Solicitudes.
* Matches.
* Feedback.

## V2

* Chat.
* Notificaciones.
* Reputación.
* Favoritos.
* Invitaciones.

## V3

* IA para recomendaciones.
* Eventos.
* Entrenamientos grupales.
* Rutinas compartidas.
* Integración con gimnasios.
* Integración con wearables.

---

# Definición de éxito

El producto habrá demostrado valor cuando dos personas desconocidas:

1. Se registren.
2. Se encuentren mediante el sistema.
3. Acepten el contacto.
4. Queden para entrenar.
5. Confirmen posteriormente que repetirían la experiencia.
