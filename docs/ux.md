# GymBuddy — UX Specification

Version: 0.1
Scope: MVP
Primary platform: Mobile web
Secondary platform: Desktop web

---

# Objetivo

Este documento define la experiencia de usuario del MVP de GymBuddy.

Es la fuente de verdad para:

* Estructura de navegación.
* Pantallas.
* Flujos.
* Componentes.
* Formularios.
* Mensajes.
* Estados vacíos.
* Estados de carga.
* Errores.
* Accesibilidad.
* Comportamiento responsive.

Lovable debe revisar este documento antes de crear o modificar interfaces.

---

# Principios de experiencia

GymBuddy debe sentirse:

* Simple.
* Seguro.
* Cercano.
* Directo.
* Rápido.
* No invasivo.

El producto no debe parecer:

* Una aplicación de citas.
* Una red social.
* Una aplicación de culturismo extremo.
* Un marketplace de entrenadores.
* Un feed de contenido fitness.

Cada pantalla debe tener un objetivo principal.

Cada acción principal debe ser evidente.

---

# Enfoque mobile first

La interfaz se diseña primero para teléfonos móviles.

Resolución de referencia:

```text
390 × 844 px
```

La aplicación también debe funcionar correctamente en:

* Teléfonos pequeños.
* Teléfonos grandes.
* Tablets.
* Portátiles.
* Monitores de escritorio.

En escritorio, el contenido principal debe tener un ancho máximo para evitar líneas demasiado largas y pantallas vacías.

Ancho máximo recomendado:

```text
1120 px
```

Para formularios y contenido textual:

```text
640 px
```

---

# Idioma

El idioma inicial del producto será español.

Todos los textos visibles deben estar en español.

Los identificadores internos, nombres de variables, endpoints y enums deben mantenerse en inglés.

Ejemplo:

Interfaz:

```text
Nivel intermedio
```

API:

```text
INTERMEDIATE
```

---

# Tono de comunicación

El tono debe ser:

* Claro.
* Humano.
* Tranquilo.
* Respetuoso.
* Sin exageraciones.
* Sin presión.

Evitar mensajes como:

```text
¡Tu compañero perfecto te está esperando!
```

Preferir:

```text
Encuentra personas compatibles para entrenar en tu gimnasio.
```

Evitar términos que sugieran citas:

* Match romántico.
* Swipe.
* Me gusta.
* Cita.
* Conquista.
* Química.

Aunque internamente se use el término `match`, en la interfaz se puede utilizar:

* Conexión.
* Compañero.
* Solicitud.
* Persona compatible.

---

# Identidad visual

La interfaz debe transmitir actividad física sin recurrir a una estética agresiva.

## Recomendaciones

* Fondos limpios.
* Espaciado amplio.
* Tarjetas sencillas.
* Bordes redondeados moderados.
* Tipografía legible.
* Iconografía clara.
* Fotografías naturales cuando se utilicen.

## Evitar

* Exceso de negro y rojo.
* Efectos metálicos.
* Tipografías de culturismo.
* Neones.
* Animaciones constantes.
* Fotografías sexualizadas.
* Cuerpos irreales como recurso principal.
* Interfaces visualmente similares a Tinder.

---

# Sistema de diseño

Debe existir un conjunto pequeño y consistente de componentes reutilizables.

## Componentes base

* Button
* IconButton
* Input
* Textarea
* Select
* Checkbox
* RadioGroup
* Switch
* Badge
* Avatar
* Card
* Modal
* Drawer
* Alert
* Toast
* Skeleton
* EmptyState
* Spinner
* Tabs
* Pagination
* Stepper
* ConfirmDialog

## Variantes de botón

### Primary

Para la acción principal de una pantalla.

Ejemplos:

* Crear cuenta.
* Guardar perfil.
* Enviar solicitud.
* Aceptar.

Solo debe existir un botón primario dominante por sección.

### Secondary

Para acciones alternativas.

Ejemplos:

* Volver.
* Editar.
* Ver perfil.

### Destructive

Para acciones sensibles.

Ejemplos:

* Bloquear.
* Eliminar cuenta.
* Rechazar cuando deba destacarse como acción destructiva.

### Ghost

Para acciones de bajo énfasis.

Ejemplos:

* Cancelar.
* Omitir por ahora.
* Cerrar.

---

# Accesibilidad

El MVP debe aspirar a cumplir WCAG 2.1 AA.

## Requisitos mínimos

* Contraste suficiente.
* Navegación por teclado.
* Focus visible.
* Labels asociados a inputs.
* Mensajes de error vinculados al campo.
* Botones con nombres accesibles.
* No depender únicamente del color.
* Texto alternativo en imágenes.
* Orden lógico de tabulación.
* Modales con gestión correcta del foco.
* Tamaño táctil mínimo aproximado de 44 × 44 px.
* Respeto por `prefers-reduced-motion`.

Los iconos sin texto deben incluir `aria-label`.

Ejemplo:

```text
aria-label="Cerrar"
```

---

# Estructura de navegación

La navegación depende del estado del usuario.

---

# Navegación pública

Disponible sin autenticación:

```text
Inicio
Cómo funciona
Seguridad
Iniciar sesión
Crear cuenta
```

En móvil debe mostrarse:

* Logotipo.
* Botón de acceso.
* Menú colapsable si es necesario.

La landing debe mantener una navegación mínima.

---

# Navegación privada

Disponible tras iniciar sesión.

## Navegación móvil inferior

Se recomienda una barra inferior con cuatro destinos:

```text
Buscar
Solicitudes
Compañeros
Perfil
```

Cada elemento debe incluir:

* Icono.
* Etiqueta textual.
* Estado activo claramente visible.

## Navegación de escritorio

Usar una barra lateral o navegación superior persistente.

Destinos:

```text
Buscar compañeros
Solicitudes
Mis compañeros
Mi perfil
```

Opciones secundarias dentro del menú de usuario:

```text
Configuración
Usuarios bloqueados
Ayuda y seguridad
Cerrar sesión
```

---

# Protección de rutas

## Rutas públicas

```text
/
/login
/register
/forgot-password
/reset-password
/privacy
/terms
/safety
```

## Rutas privadas

```text
/onboarding
/discover
/requests
/connections
/connections/:matchId
/profile
/profile/edit
/profile/gyms
/profile/availability
/settings
/settings/account
/settings/privacy
/settings/blocked-users
```

## Rutas administrativas

```text
/admin
/admin/reports
/admin/users
/admin/gyms
```

---

# Redirecciones

## Usuario no autenticado

Si intenta acceder a una ruta privada:

```text
redirigir a /login
```

Tras iniciar sesión, puede regresar a la ruta original si sigue siendo válida.

## Usuario autenticado sin perfil completo

Debe ser dirigido a:

```text
/onboarding
```

No debe poder acceder al buscador hasta completar los mínimos obligatorios.

## Usuario autenticado con perfil completo

Si intenta acceder a `/login` o `/register`, redirigir a:

```text
/discover
```

## Usuario suspendido

Mostrar una pantalla específica.

No permitir navegación privada normal.

## Usuario eliminado

Invalidar sesión y dirigir a la página pública.

---

# Landing page

Ruta:

```text
/
```

## Objetivo

Explicar el producto y conseguir registros.

## Estructura recomendada

### Cabecera

* Logotipo GymBuddy.
* Enlace “Iniciar sesión”.
* Botón “Crear cuenta”.

### Hero

Título:

```text
Encuentra compañía para entrenar en tu gimnasio
```

Subtítulo:

```text
Conecta con personas compatibles por gimnasio, horario, nivel y objetivos.
```

Acción principal:

```text
Buscar compañero
```

Acción secundaria:

```text
Ver cómo funciona
```

Añadir una indicación de confianza:

```text
Tu contacto solo se comparte cuando ambos aceptáis.
```

### Cómo funciona

Tres pasos:

1. Completa tu perfil.
2. Descubre personas compatibles.
3. Aceptad la conexión y organizad el entrenamiento.

### Casos de uso

Mostrar situaciones concretas:

* Acabas de mudarte.
* Tus horarios no coinciden con tus amigos.
* Necesitas un spotter.
* Quieres recuperar la constancia.
* Estás unos días en otra ciudad.

### Seguridad

Explicar de forma breve:

* Aceptación mutua.
* Bloqueo.
* Reporte.
* Contacto oculto hasta conectar.
* Recomendación de quedar dentro del gimnasio.

### Cierre

Título:

```text
Entrenar acompañado debería ser más fácil
```

Botón:

```text
Crear mi perfil
```

### Footer

* Privacidad.
* Términos.
* Seguridad.
* Contacto.
* Copyright.

---

# Registro

Ruta:

```text
/register
```

## Objetivo

Crear una cuenta con la menor fricción posible.

## Campos

* Correo electrónico.
* Contraseña.
* Confirmación de contraseña.
* Aceptación de términos.
* Aceptación de política de privacidad.

## Reglas de interfaz

* Mostrar requisitos de contraseña antes de enviar.
* Permitir mostrar u ocultar contraseña.
* Validar sin esperar exclusivamente al envío.
* No bloquear al usuario mientras escribe.
* Mostrar errores junto al campo.
* Mantener los valores válidos tras un error.
* Desactivar el botón únicamente durante el envío.

## Acción principal

```text
Crear cuenta
```

## Acción secundaria

```text
Ya tengo una cuenta
```

## Mensaje posterior

Tras registro correcto:

```text
Cuenta creada. Ahora vamos a preparar tu perfil.
```

Después, redirigir a onboarding.

---

# Inicio de sesión

Ruta:

```text
/login
```

## Campos

* Correo electrónico.
* Contraseña.

## Acciones

Principal:

```text
Iniciar sesión
```

Secundarias:

```text
He olvidado mi contraseña
Crear una cuenta
```

## Error de credenciales

Mensaje:

```text
El correo o la contraseña no son correctos.
```

No indicar cuál de los dos datos es incorrecto.

---

# Recuperación de contraseña

Ruta:

```text
/forgot-password
```

## Campo

* Correo electrónico.

## Acción principal

```text
Enviar instrucciones
```

## Confirmación

Mostrar siempre:

```text
Si existe una cuenta asociada a ese correo, recibirás instrucciones para restablecer la contraseña.
```

No revelar si el correo está registrado.

---

# Onboarding

Ruta:

```text
/onboarding
```

## Objetivo

Completar la información mínima necesaria para el matching.

El onboarding debe dividirse en pasos cortos.

No mostrar un formulario enorme.

## Estructura

Stepper superior:

```text
1. Sobre ti
2. Entrenamiento
3. Gimnasio
4. Horarios
5. Contacto
6. Revisión
```

Mostrar progreso de forma visual y textual.

Ejemplo:

```text
Paso 2 de 6
```

Los datos deben conservarse al avanzar o retroceder.

Siempre que sea posible, guardar borradores parciales.

---

# Onboarding — Paso 1: Sobre ti

## Campos

* Nombre visible.
* Fecha de nacimiento.
* Ciudad.
* Género opcional.
* Descripción breve opcional.

## Ayudas

Nombre visible:

```text
No necesitas usar tu nombre completo.
```

Fecha de nacimiento:

```text
Debes tener al menos 18 años.
```

Descripción:

```text
Cuéntales brevemente a otros usuarios cómo entrenas.
```

## Validaciones

* Nombre visible obligatorio.
* Fecha válida.
* Mayoría de edad.
* Ciudad obligatoria.
* Bio con máximo de caracteres visible.

Ejemplo:

```text
84/300
```

---

# Onboarding — Paso 2: Entrenamiento

## Campos

### Nivel

Opciones:

```text
Principiante
Intermedio
Avanzado
```

Cada opción puede incluir una explicación breve.

Principiante:

```text
Estoy empezando o todavía necesito orientación.
```

Intermedio:

```text
Entreno con regularidad y conozco los ejercicios principales.
```

Avanzado:

```text
Tengo experiencia sólida y objetivos específicos.
```

### Objetivo principal

Opciones:

* Ganar masa muscular.
* Perder grasa.
* Mejorar fuerza.
* Powerlifting.
* Forma física general.
* Entrenamiento funcional.
* Volver a entrenar.
* Otro.

### Razón para buscar compañero

Opciones:

* Soy nuevo en el gimnasio.
* Me cuesta entrenar solo.
* Busco motivación.
* Necesito un spotter.
* Estoy de viaje.
* Me he mudado recientemente.
* Mis amigos no entrenan.
* Nuestros horarios no coinciden.
* Otro.

### Preferencia

Pregunta:

```text
¿Cómo prefieres entrenar?
```

Opciones:

* Siempre acompañado.
* A veces acompañado.
* Solo para entrenamientos concretos.
* Normalmente solo, pero quiero tener la opción.

La última opción es importante para usuarios que no buscan compañía constante.

---

# Onboarding — Paso 3: Gimnasio

## Objetivo

Seleccionar al menos un gimnasio.

## Interfaz

* Campo de búsqueda.
* Resultados por nombre y ciudad.
* Tarjetas de gimnasio.
* Indicador de gimnasio verificado.
* Acción para añadir uno no encontrado.

## Resultado

Cada tarjeta debe mostrar:

* Nombre.
* Ciudad.
* Dirección, si existe.
* Estado verificado, si aplica.

## Acción principal

```text
Seleccionar gimnasio
```

## Gimnasio no encontrado

Enlace:

```text
No encuentro mi gimnasio
```

Abrir formulario con:

* Nombre.
* Ciudad.
* Dirección opcional.
* Código postal opcional.

Mensaje:

```text
Lo añadiremos como gimnasio pendiente de verificación.
```

## Gimnasio principal

Cuando haya varios:

```text
Usar como gimnasio principal
```

Para el MVP, durante onboarding puede seleccionarse únicamente uno y permitir más desde el perfil.

---

# Onboarding — Paso 4: Horarios

## Objetivo

Registrar al menos una franja habitual.

## Interfaz recomendada

Selector por días:

```text
L M X J V S D
```

Al seleccionar un día, permitir añadir:

* Hora de inicio.
* Hora de fin.

Ejemplo:

```text
Lunes · 18:00–20:00
```

Permitir varias franjas por día.

## Acciones

```text
Añadir horario
Eliminar
Copiar a otros días
```

La función “Copiar a otros días” es útil, pero puede posponerse si complica el MVP.

## Mensaje de ayuda

```text
No tiene que ser exacto. Indica cuándo sueles entrenar normalmente.
```

## Validaciones

* Al menos una franja.
* Inicio anterior al final.
* No permitir franjas vacías.
* Mostrar zona horaria si no es la esperada.

---

# Onboarding — Paso 5: Contacto

## Objetivo

Configurar el medio que se compartirá tras una aceptación mutua.

## Tipos iniciales

* WhatsApp.
* Telegram.
* Instagram.
* Teléfono.
* Otro.

## Mensaje de privacidad

Debe aparecer de forma destacada:

```text
Este dato permanecerá oculto. Solo lo verá una persona cuando ambos aceptéis conectar.
```

## Validación

La interfaz debe adaptarse al tipo seleccionado.

Ejemplos:

WhatsApp:

```text
+34 600 000 000
```

Telegram:

```text
@usuario
```

Instagram:

```text
@usuario
```

No mostrar públicamente este valor en ninguna vista anterior al match.

---

# Onboarding — Paso 6: Revisión

## Objetivo

Permitir revisar el perfil antes de publicarlo.

## Mostrar

* Nombre visible.
* Ciudad.
* Nivel.
* Objetivo.
* Motivo.
* Preferencia.
* Gimnasio.
* Horarios.
* Medio de contacto parcialmente oculto.

Ejemplo:

```text
Telegram · @mar••••••
```

## Acciones

Principal:

```text
Publicar perfil
```

Secundaria:

```text
Editar información
```

## Confirmación

```text
Tu perfil ya está listo. Vamos a buscar personas compatibles.
```

Redirigir a:

```text
/discover
```

---

# Buscar compañeros

Ruta:

```text
/discover
```

## Objetivo

Mostrar personas compatibles y facilitar una solicitud.

## Cabecera

Título:

```text
Personas compatibles
```

Subtítulo dinámico:

```text
En Gym Centro · según tus horarios habituales
```

## Filtros

Filtros iniciales:

* Gimnasio.
* Día.
* Nivel.
* Objetivo.
* Compatibilidad mínima.

En móvil, abrir filtros en un drawer.

No mostrar demasiados filtros inicialmente.

## Tarjeta de candidato

Cada tarjeta debe incluir:

* Avatar.
* Nombre visible.
* Edad aproximada o edad.
* Nivel.
* Objetivo principal.
* Gimnasio compartido.
* Horarios coincidentes.
* Puntuación o etiqueta de compatibilidad.
* Razones principales.
* Botón de ver perfil.
* Botón de enviar solicitud.

Ejemplo:

```text
Carlos, 34
Nivel avanzado · Powerlifting

Coincidís:
Lunes de 18:30 a 20:00

Buena compatibilidad
• Mismo gimnasio
• Horario compatible
• Objetivos relacionados
```

## Compatibilidad

Evitar presentar el algoritmo como científicamente preciso.

No utilizar:

```text
82 % compatible para ti
```

como promesa absoluta.

Preferir etiquetas:

```text
Compatibilidad alta
Compatibilidad media
```

La puntuación numérica puede mostrarse de forma secundaria si resulta útil.

## Acción principal

```text
Enviar solicitud
```

## Acción secundaria

```text
Ver perfil
```

---

# Detalle de candidato

Ruta recomendada:

```text
/discover/:userId
```

## Mostrar

* Avatar.
* Nombre.
* Edad.
* Ciudad.
* Bio.
* Nivel.
* Objetivo.
* Motivo para buscar compañero.
* Preferencia.
* Gimnasio compartido.
* Horarios compatibles.
* Razones de compatibilidad.
* Marcas deportivas, solo si el usuario decidió mostrarlas.

## No mostrar

* Correo.
* Fecha de nacimiento.
* Teléfono.
* Telegram.
* Instagram.
* Dirección exacta.
* Último acceso exacto.

## Acción principal

```text
Enviar solicitud
```

## Acción secundaria

```text
Volver a resultados
```

## Seguridad

Incluir menú secundario:

```text
Bloquear
Reportar
```

Estas acciones no deben competir visualmente con la acción principal.

---

# Enviar solicitud

Puede utilizarse un modal o una pantalla breve.

## Contenido

Título:

```text
Enviar solicitud a Carlos
```

Información:

```text
Entrenáis en Gym Centro y coincidís los lunes por la tarde.
```

Campo opcional:

```text
Añade un mensaje
```

Placeholder:

```text
Por ejemplo: ¿Te viene bien entrenar el lunes sobre las 19:00?
```

Máximo:

```text
250 caracteres
```

## Acciones

Principal:

```text
Enviar solicitud
```

Secundaria:

```text
Cancelar
```

## Confirmación

Toast:

```text
Solicitud enviada.
```

La tarjeta debe actualizarse inmediatamente a:

```text
Solicitud pendiente
```

Evitar envíos duplicados.

---

# Estado sin candidatos

Cuando no haya resultados, no mostrar una pantalla vacía.

## Mensaje

```text
Todavía no hemos encontrado personas compatibles.
```

## Explicación

```text
Puede que aún no haya suficientes usuarios en tu gimnasio o que los horarios no coincidan.
```

## Acciones

* Cambiar filtros.
* Añadir más horarios.
* Revisar gimnasio.
* Compartir GymBuddy.

Acción principal:

```text
Ampliar mis horarios
```

Acción secundaria:

```text
Limpiar filtros
```

No prometer que aparecerán candidatos pronto.

---

# Solicitudes

Ruta:

```text
/requests
```

## Estructura

Tabs:

```text
Recibidas
Enviadas
```

Opcionalmente:

```text
Historial
```

---

# Solicitudes recibidas

Cada tarjeta debe mostrar:

* Persona.
* Gimnasio.
* Compatibilidad.
* Mensaje.
* Fecha de envío.
* Fecha de expiración, si está cercana.
* Acciones.

## Acciones

Principal:

```text
Aceptar
```

Secundaria:

```text
Rechazar
```

También:

```text
Ver perfil
```

## Aceptación

Antes de aceptar, mostrar confirmación breve:

```text
Al aceptar, ambos podréis ver vuestros datos de contacto.
```

Acciones:

```text
Aceptar y compartir contacto
Cancelar
```

## Resultado

Mensaje:

```text
Conexión creada. Ya podéis organizar vuestro entrenamiento.
```

Redirigir al detalle del match.

## Rechazo

No exigir motivo.

Confirmación ligera:

```text
¿Quieres rechazar esta solicitud?
```

No usar mensajes culpabilizadores.

---

# Solicitudes enviadas

Cada tarjeta debe mostrar:

* Persona.
* Gimnasio.
* Fecha.
* Estado.
* Acción de cancelar cuando siga pendiente.

Estados visibles:

```text
Pendiente
Aceptada
Rechazada
Cancelada
Expirada
```

## Acción

```text
Cancelar solicitud
```

La cancelación debe requerir confirmación.

---

# Mis compañeros

Ruta:

```text
/connections
```

## Objetivo

Mostrar conexiones activas e históricas.

## Tabs

```text
Activos
Archivados
```

## Tarjeta de conexión

Mostrar:

* Avatar.
* Nombre.
* Gimnasio.
* Fecha aproximada de conexión.
* Próximo entrenamiento si existe.
* Estado de feedback pendiente.
* Acción “Ver conexión”.

No mostrar directamente el contacto en el listado si puede evitarse.

---

# Detalle de conexión

Ruta:

```text
/connections/:matchId
```

## Mostrar

### Persona

* Avatar.
* Nombre.
* Nivel.
* Objetivo.
* Bio breve.

### Gimnasio

* Nombre.
* Ciudad.
* Dirección si existe.

### Contacto

Mostrar únicamente si está autorizado.

Título:

```text
Contacto compartido
```

Ejemplo:

```text
Telegram
@carlos_entrena
```

Acción contextual:

```text
Abrir Telegram
```

Para WhatsApp:

```text
Abrir WhatsApp
```

Los enlaces externos deben advertir visualmente que se abrirá otra aplicación.

### Seguridad

Texto breve:

```text
Para el primer entrenamiento, queda dentro del gimnasio y evita compartir información personal innecesaria.
```

### Entrenamiento previsto

Permitir añadir o modificar fecha.

Acción:

```text
Planificar entrenamiento
```

### Feedback

Si la fecha ya pasó y no existe feedback:

```text
¿Llegasteis a entrenar?
```

Acciones:

```text
Sí
No
```

### Opciones secundarias

* Archivar conexión.
* Bloquear usuario.
* Reportar usuario.

---

# Planificar entrenamiento

Puede abrirse en modal o drawer.

## Campos

* Fecha.
* Hora.

El gimnasio ya está asociado al match.

## Acción principal

```text
Guardar fecha
```

## Mensaje

```text
Esta fecha es orientativa. Confírmala directamente con tu compañero.
```

No crear un sistema complejo de calendario en el MVP.

---

# Feedback de entrenamiento

## Primera pregunta

```text
¿Llegasteis a entrenar juntos?
```

Opciones:

```text
Sí
No
```

## Si la respuesta es sí

Preguntar:

```text
¿Volverías a entrenar con esta persona?
```

Opciones:

* Sí.
* Quizá.
* No.

Rating opcional:

```text
¿Cómo fue la experiencia?
```

Escala:

```text
1 a 5
```

Comentario opcional:

```text
Añade un comentario privado
```

Debe indicarse que el comentario no será público en el MVP.

## Si la respuesta es no

Comentario opcional:

```text
¿Qué ocurrió?
```

Opciones futuras posibles:

* No coincidimos.
* Cambiamos de planes.
* La otra persona no respondió.
* Otro.

Para el MVP puede utilizarse texto libre opcional.

## Acción

```text
Enviar respuesta
```

## Confirmación

```text
Gracias. Tu respuesta nos ayuda a mejorar GymBuddy.
```

---

# Perfil propio

Ruta:

```text
/profile
```

## Mostrar

* Avatar.
* Nombre.
* Ciudad.
* Estado del perfil.
* Visibilidad.
* Nivel.
* Objetivo.
* Motivo.
* Preferencia.
* Bio.
* Gimnasios.
* Horarios.
* Contacto oculto parcialmente.
* Marcas opcionales.

## Acciones

* Editar perfil.
* Gestionar gimnasios.
* Gestionar horarios.
* Cambiar contacto.
* Activar o pausar visibilidad.

## Indicador de visibilidad

Cuando está visible:

```text
Tu perfil aparece en las búsquedas compatibles.
```

Cuando está oculto:

```text
Tu perfil está en pausa y no aparecerá en nuevas búsquedas.
```

Acción:

```text
Pausar perfil
```

o:

```text
Reactivar perfil
```

---

# Edición de perfil

Ruta:

```text
/profile/edit
```

## Reglas

* Dividir por secciones.
* No obligar a repetir onboarding.
* Guardar únicamente cambios.
* Mostrar confirmación.
* Recalcular perfil completo.
* Avisar cuando un cambio reduzca resultados.

Ejemplo:

```text
Eliminar todos tus horarios hará que dejes de aparecer como perfil completo.
```

---

# Gestión de gimnasios

Ruta:

```text
/profile/gyms
```

## Mostrar

* Lista de gimnasios.
* Indicador de principal.
* Estado de verificación.
* Acción para añadir.
* Acción para eliminar.
* Acción para establecer como principal.

## Confirmación de eliminación

```text
¿Quieres eliminar este gimnasio de tu perfil?
```

Si es el único:

```text
Necesitas al menos un gimnasio para buscar compañeros.
```

---

# Gestión de disponibilidad

Ruta:

```text
/profile/availability
```

## Mostrar

Agrupación por día.

Ejemplo:

```text
Lunes
18:00–20:00
```

Acciones:

* Añadir franja.
* Editar.
* Eliminar.
* Copiar a otros días, opcional.

Mostrar siempre:

```text
Cuantos más horarios añadas, más posibilidades tendrás de encontrar personas compatibles.
```

Sin usar presión excesiva.

---

# Configuración

Ruta:

```text
/settings
```

## Secciones

* Cuenta.
* Privacidad.
* Seguridad.
* Usuarios bloqueados.
* Ayuda.
* Cerrar sesión.

---

# Configuración de cuenta

Ruta:

```text
/settings/account
```

## Acciones

* Cambiar correo.
* Cambiar contraseña.
* Eliminar cuenta.

La eliminación debe aparecer separada en una zona de peligro.

---

# Eliminar cuenta

Debe requerir:

* Contraseña actual.
* Confirmación textual.
* Explicación clara de consecuencias.

Mensaje:

```text
Tu perfil dejará de aparecer, se cancelarán las solicitudes pendientes y se cerrarán tus sesiones.
```

Confirmación solicitada:

```text
Escribe ELIMINAR para continuar
```

Botón:

```text
Eliminar mi cuenta
```

No utilizar patrones engañosos.

---

# Usuarios bloqueados

Ruta:

```text
/settings/blocked-users
```

## Mostrar

* Avatar.
* Nombre.
* Fecha de bloqueo.
* Acción para desbloquear.

## Desbloqueo

Confirmación:

```text
Desbloquear no restaurará solicitudes ni conexiones anteriores.
```

Acción:

```text
Desbloquear
```

---

# Bloquear usuario

Debe estar disponible desde:

* Perfil público.
* Solicitud.
* Conexión.

## Confirmación

```text
No volveréis a aparecer el uno para el otro y se cancelarán las solicitudes pendientes.
```

Campo de motivo opcional.

Acciones:

```text
Bloquear usuario
Cancelar
```

Después de bloquear:

* Cerrar la pantalla actual.
* Eliminar al usuario de la interfaz.
* Mostrar confirmación.

```text
Usuario bloqueado.
```

---

# Reportar usuario

Disponible desde:

* Perfil.
* Solicitud.
* Conexión.

## Formulario

Motivo obligatorio:

* Comportamiento inapropiado.
* Acoso.
* Perfil falso.
* Spam.
* Comportamiento inseguro.
* Otro.

Descripción opcional.

Mensaje:

```text
El usuario no sabrá quién ha enviado el reporte.
```

Acción:

```text
Enviar reporte
```

Confirmación:

```text
Reporte enviado. Revisaremos la información.
```

Reportar no debe bloquear automáticamente sin informar al usuario.

Después del reporte, ofrecer explícitamente la opción de bloquear.

---

# Cuenta suspendida

Pantalla específica.

## Contenido

Título:

```text
Tu cuenta está suspendida
```

Texto:

```text
No puedes utilizar GymBuddy en este momento. Revisa la información recibida o contacta con soporte si crees que se trata de un error.
```

Acciones:

* Contactar con soporte.
* Cerrar sesión.

No mostrar el contenido privado de otros usuarios.

---

# Panel de administración

El diseño administrativo puede ser funcional y sencillo.

No necesita compartir toda la estética de la aplicación de usuario.

## Dashboard

Mostrar:

* Visitas.
* Registros.
* Perfiles completos.
* Solicitudes.
* Conexiones.
* Entrenamientos confirmados.
* Conversión del embudo.

## Reportes

Tabla con:

* Fecha.
* Motivo.
* Usuario reportado.
* Estado.
* Acción de revisar.

## Usuarios

Búsqueda por:

* ID.
* Correo.
* Nombre visible.

Acciones:

* Ver estado.
* Suspender.
* Reactivar.

## Gimnasios

Acciones:

* Buscar.
* Editar.
* Verificar.
* Rechazar.

Todas las acciones administrativas sensibles deben requerir confirmación.

---

# Estados de carga

Nunca dejar una pantalla sin respuesta visual.

## Carga inicial

Usar skeletons para:

* Tarjetas de candidatos.
* Solicitudes.
* Conexiones.
* Perfil.

## Acciones

Durante una acción:

* Deshabilitar el botón correspondiente.
* Mostrar spinner.
* Mantener la etiqueta comprensible.

Ejemplo:

```text
Enviando...
```

No bloquear toda la interfaz si solo se está actualizando una sección.

---

# Estados vacíos

Cada listado debe tener un estado vacío específico.

## Sin solicitudes recibidas

```text
No tienes solicitudes pendientes.
```

## Sin solicitudes enviadas

```text
Todavía no has enviado ninguna solicitud.
```

## Sin conexiones

```text
Aún no tienes compañeros conectados.
```

Acción:

```text
Buscar personas compatibles
```

## Sin usuarios bloqueados

```text
No has bloqueado a ningún usuario.
```

## Sin horarios

```text
Añade al menos un horario para encontrar personas compatibles.
```

Los estados vacíos deben orientar, no culpar.

---

# Manejo de errores

## Error de campo

Mostrar debajo del campo.

Ejemplo:

```text
Introduce una fecha válida.
```

## Error de formulario

Mostrar un resumen solo cuando sea útil.

## Error de red

```text
No hemos podido conectar con el servidor. Comprueba tu conexión e inténtalo de nuevo.
```

## Error inesperado

```text
Ha ocurrido un problema inesperado. Inténtalo de nuevo.
```

## Recurso no disponible

```text
Este perfil ya no está disponible.
```

## Acción en conflicto

Ejemplo al aceptar una solicitud ya cancelada:

```text
Esta solicitud ya no está pendiente.
```

Después, actualizar la interfaz.

No mostrar códigos técnicos al usuario final.

Puede incluirse un identificador de soporte en detalles:

```text
Referencia: ABC123
```

---

# Toasts

Usar toasts para confirmaciones breves.

Ejemplos:

```text
Perfil actualizado.
Solicitud enviada.
Solicitud cancelada.
Conexión archivada.
Usuario bloqueado.
```

No usar toasts para información crítica que deba permanecer visible.

Los errores importantes deben aparecer cerca de la acción o contenido afectado.

---

# Modales y drawers

## Modal

Usar para:

* Confirmaciones.
* Acciones breves.
* Eliminaciones.
* Aceptar solicitudes.

## Drawer móvil

Usar para:

* Filtros.
* Menús secundarios.
* Formularios breves.

No introducir flujos largos dentro de un modal.

El usuario debe poder cerrar con:

* Botón visible.
* Tecla Escape.
* Acción cancelar.

No cerrar accidentalmente un formulario con cambios sin advertencia.

---

# Formularios

## Reglas generales

* Etiquetas visibles.
* Placeholders solo como ayuda, no como label.
* Campos agrupados.
* Errores específicos.
* Estado de envío.
* Prevención de doble envío.
* Autocompletado correcto cuando aplique.
* Teclado móvil adecuado.

Ejemplos:

Correo:

```text
inputmode="email"
autocomplete="email"
```

Teléfono:

```text
inputmode="tel"
autocomplete="tel"
```

Contraseña actual:

```text
autocomplete="current-password"
```

Nueva contraseña:

```text
autocomplete="new-password"
```

---

# Validación progresiva

No mostrar errores agresivos antes de que el usuario interactúe.

Secuencia recomendada:

1. El usuario entra en el campo.
2. Escribe.
3. Sale del campo.
4. Se muestra la validación si existe error.
5. Al enviar, se validan todos los campos.

Los errores del backend deben mapearse al campo correspondiente cuando sea posible.

---

# Navegación y pérdida de cambios

Si el usuario intenta salir de un formulario con cambios no guardados:

```text
Tienes cambios sin guardar. ¿Quieres salir?
```

Acciones:

```text
Seguir editando
Salir sin guardar
```

No mostrar esta confirmación cuando no existan cambios.

---

# Responsive desktop

En escritorio:

* Barra lateral o cabecera persistente.
* Listado y detalle pueden convivir en dos columnas.
* Los filtros pueden mostrarse en lateral.
* Las tarjetas no deben estirarse en exceso.
* Los formularios deben conservar un ancho legible.

Ejemplo para búsqueda:

```text
Filtros | Resultados | Vista previa opcional
```

La versión móvil sigue siendo la prioridad.

---

# Rendimiento percibido

Para mejorar la sensación de velocidad:

* Usar skeletons.
* Mantener datos previos durante recargas no destructivas.
* Actualizar acciones sencillas de forma optimista cuando sea seguro.
* Revertir y explicar si la operación falla.
* Evitar recargar la página completa.
* Precargar rutas críticas cuando sea razonable.

No utilizar actualizaciones optimistas en operaciones críticas como:

* Aceptar una solicitud.
* Bloquear usuario.
* Eliminar cuenta.

En estos casos, esperar confirmación del servidor.

---

# Privacidad visual

La interfaz nunca debe mostrar accidentalmente datos sensibles.

## Antes de la conexión

Ocultar:

* Teléfono.
* Telegram.
* Instagram.
* Correo.
* Fecha de nacimiento completa.

## Después de la conexión

Mostrar únicamente el medio de contacto configurado.

## Capturas y vistas previas

Evitar incluir contacto en:

* Notificaciones visibles en pantalla bloqueada.
* Metadatos.
* Títulos de página.
* URLs.
* Logs del navegador.

---

# Seguridad percibida

La confianza debe explicarse en los momentos relevantes.

## Antes de configurar contacto

```text
Permanecerá oculto hasta que ambos aceptéis.
```

## Antes de aceptar una solicitud

```text
Al aceptar, ambos podréis ver el contacto configurado.
```

## En la conexión

```text
Queda dentro del gimnasio y comunica cualquier comportamiento inseguro.
```

No saturar al usuario con advertencias constantes.

---

# Notificaciones

Las notificaciones push y el correo transaccional avanzado quedan fuera del MVP.

Dentro de la aplicación se usarán indicadores visuales:

* Número de solicitudes pendientes.
* Feedback pendiente.
* Cambios de estado.

Ejemplo en navegación:

```text
Solicitudes · 2
```

Los contadores deben actualizarse al completar las acciones.

---

# SEO y metadatos

Solo las páginas públicas requieren optimización SEO.

## Landing

Título recomendado:

```text
GymBuddy — Encuentra compañero de entrenamiento
```

Descripción:

```text
Conecta con personas compatibles para entrenar en el mismo gimnasio según horarios, nivel y objetivos.
```

Las páginas privadas deben impedir indexación.

---

# Analítica de UX

Las interacciones relevantes deben registrar eventos sin incluir datos sensibles.

Ejemplos:

* Vista de landing.
* Inicio de registro.
* Registro completado.
* Paso de onboarding completado.
* Búsqueda iniciada.
* Filtros aplicados.
* Perfil de candidato visto.
* Solicitud enviada.
* Solicitud aceptada.
* Contacto revelado.
* Feedback enviado.

No registrar:

* Texto de mensajes.
* Contacto.
* Bio completa.
* Descripciones de reportes.
* Contraseñas.
* Correos.

---

# Componentes específicos

## MatchCard

Responsabilidad:

Mostrar un candidato resumido.

Props conceptuales:

```text
candidate
compatibility
requestState
onView
onRequest
```

Estados:

* Disponible.
* Solicitud pendiente.
* Ya conectado.
* No disponible.

## RequestCard

Responsabilidad:

Mostrar una solicitud recibida o enviada.

Estados:

* Pendiente.
* Aceptada.
* Rechazada.
* Cancelada.
* Expirada.

## ConnectionCard

Responsabilidad:

Mostrar una conexión.

Información:

* Usuario.
* Gimnasio.
* Estado.
* Próximo entrenamiento.
* Feedback pendiente.

## AvailabilityPicker

Responsabilidad:

Crear y editar franjas horarias.

Debe ser usable con teclado y móvil.

## GymSelector

Responsabilidad:

Buscar, seleccionar o proponer un gimnasio.

Debe distinguir claramente:

* Verificado.
* No verificado.
* Propuesto por usuario.

## ContactField

Responsabilidad:

Adaptar la validación y ayuda al tipo de contacto elegido.

## SafetyActions

Menú reutilizable con:

* Bloquear.
* Reportar.

Debe incluir confirmaciones y no exponer la identidad del reportante.

---

# Microcopy recomendada

## Acciones

```text
Crear cuenta
Guardar cambios
Continuar
Volver
Enviar solicitud
Aceptar y compartir contacto
Rechazar
Cancelar solicitud
Buscar compañeros
Ver conexión
Planificar entrenamiento
Enviar respuesta
```

## Evitar

```text
Submit
OK
Proceed
Match now
Like
Dislike
Swipe
```

## Mensajes positivos

```text
Perfil actualizado.
Solicitud enviada.
Ya podéis poneros en contacto.
Gracias por compartir tu experiencia.
```

## Mensajes neutros

```text
No hay resultados con estos filtros.
Esta solicitud ha expirado.
Este perfil ya no está disponible.
```

---

# Animaciones

Las animaciones deben ser discretas.

Permitidas:

* Transiciones de 150–250 ms.
* Apertura de modal.
* Cambio de tab.
* Feedback visual de botón.
* Aparición suave de toast.

Evitar:

* Confeti.
* Tarjetas volando.
* Swipes de citas.
* Animaciones continuas.
* Efectos que retrasen acciones.

Respetar:

```text
prefers-reduced-motion
```

---

# Criterios de aceptación de UX

La experiencia se considera correctamente implementada cuando:

* Se puede completar el registro desde móvil.
* El onboarding está dividido en pasos claros.
* Los datos se conservan al retroceder.
* El usuario entiende cuándo se comparte su contacto.
* Ningún contacto se muestra antes de una conexión aceptada.
* La búsqueda explica por qué una persona es compatible.
* Las solicitudes tienen estados claros.
* Aceptar y rechazar son acciones diferenciadas.
* Los estados vacíos ofrecen una acción útil.
* Todos los formularios muestran errores comprensibles.
* Las acciones destructivas requieren confirmación.
* El perfil puede pausarse.
* Bloquear elimina inmediatamente al usuario de la experiencia.
* Reportar protege la identidad del reportante.
* La aplicación es usable con teclado.
* El diseño funciona en móvil y escritorio.
* Los componentes se reutilizan.
* No se utilizan patrones visuales propios de aplicaciones de citas.
* Los datos sensibles no aparecen en URLs, logs ni vistas no autorizadas.

---

# Orden de implementación del frontend

Lovable debe construir la interfaz en este orden:

1. Sistema de diseño básico.
2. Layout público.
3. Landing.
4. Registro e inicio de sesión.
5. Layout privado.
6. Onboarding.
7. Perfil propio.
8. Gestión de gimnasios y horarios.
9. Buscador de candidatos.
10. Detalle de candidato.
11. Solicitudes.
12. Conexiones.
13. Feedback.
14. Bloqueos y reportes.
15. Configuración.
16. Panel de administración.
17. Estados de carga, error y vacío.
18. Revisión de accesibilidad.
19. Revisión responsive.
20. Tests de interfaz.

---

# Reglas para Lovable

Lovable debe:

* Implementar mobile first.
* Reutilizar componentes.
* Mantener labels visibles.
* Añadir estados de carga, vacío y error.
* Mantener una acción principal clara.
* Respetar la privacidad del contacto.
* Evitar estética de aplicación de citas.
* Usar textos en español.
* Mantener identificadores técnicos en inglés.
* Añadir confirmación en acciones destructivas.
* No usar datos falsos en producción.
* No introducir chat, feed o gamificación.
* No añadir funcionalidades fuera del MVP sin actualizar la documentación.
* Comprobar accesibilidad antes de dar una pantalla por terminada.
