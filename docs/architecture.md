# GymBuddy - Architecture

Version: 0.1

---

# Objetivo

Definir la arquitectura técnica del proyecto para mantener un código limpio, escalable y fácil de mantener.

GymBuddy se desarrolla como un MVP, pero la arquitectura debe permitir evolucionar hacia un producto completo.

---

# Principios

La arquitectura debe seguir estos principios:

* Separación de responsabilidades.
* Componentes reutilizables.
* Bajo acoplamiento.
* Alta cohesión.
* Código testeable.
* Evitar duplicidades.
* Convenciones consistentes.

---

# Arquitectura general

```text
Browser
    │
    ▼
React + TypeScript
    │
    ▼
API Backend
    │
    ▼
Services
    │
    ▼
Repositories
    │
    ▼
Prisma ORM
    │
    ▼
PostgreSQL
```

El frontend nunca accede directamente a PostgreSQL.

Toda la lógica de negocio reside en el backend.

---

# Tecnologías

Frontend

* React
* TypeScript
* TailwindCSS

Backend

* Node.js
* API REST
* Prisma

Persistencia

* PostgreSQL

---

# Organización del proyecto

```text
src/

components/
pages/
layouts/
hooks/
contexts/
services/
utils/
types/
styles/

server/

controllers/
services/
repositories/
middleware/
routes/
validators/

prisma/

schema.prisma
migrations/

docs/
```

Cada carpeta debe tener una única responsabilidad.

---

# Flujo de una petición

Ejemplo:

Usuario solicita buscar compañeros.

↓

Controller

↓

Service

↓

Repository

↓

Prisma

↓

PostgreSQL

↓

Repository

↓

Service

↓

Controller

↓

Respuesta JSON

---

# Controllers

Responsabilidades:

* Recibir la petición.
* Validar parámetros básicos.
* Llamar al Service.
* Devolver respuesta.

Nunca deben contener lógica de negocio.

---

# Services

Aquí vive toda la lógica del producto.

Ejemplos:

* Matching.
* Registro.
* Solicitudes.
* Confirmación de entrenamiento.
* Reportes.

Los Services nunca conocen detalles de HTTP.

---

# Repositories

Responsables únicamente del acceso a datos.

Ejemplos:

* Buscar usuarios.
* Crear solicitudes.
* Actualizar perfiles.
* Obtener gimnasios.

Nunca deben contener lógica de negocio.

---

# Prisma

Prisma será la única capa que acceda a PostgreSQL.

No deben existir consultas SQL repartidas por el proyecto.

---

# Componentes React

Preferir componentes pequeños.

Ejemplos:

Button

Card

Avatar

Modal

Navbar

MatchCard

GymSelector

AvailabilityPicker

ProfileForm

Cada componente debe tener una única responsabilidad.

---

# Estado de la aplicación

Priorizar estado local.

Usar contexto únicamente para:

* Usuario autenticado.
* Tema.
* Configuración global.

Evitar almacenar información temporal en contextos globales.

---

# Formularios

Todos los formularios deben:

* Validar antes de enviar.
* Mostrar errores claros.
* Ser reutilizables.

---

# Validaciones

Toda validación debe existir:

Frontend

↓

Backend

Nunca confiar únicamente en el frontend.

---

# Errores

Todos los errores deben devolver una estructura consistente.

Ejemplo:

```json
{
  "success": false,
  "message": "Profile not found"
}
```

---

# API

La API seguirá REST.

Ejemplos:

GET

POST

PATCH

DELETE

Las respuestas deben ser consistentes.

---

# Autenticación

Toda petición privada requiere autenticación.

Nunca confiar en datos enviados por el cliente.

El usuario autenticado se obtiene desde la sesión.

---

# Autorización

Cada endpoint debe comprobar permisos.

Ejemplos:

Un usuario no puede:

* editar otro perfil;
* ver contactos sin match;
* aceptar solicitudes ajenas.

---

# Seguridad

No almacenar:

* contraseñas en texto plano;
* tokens inseguros;
* datos sensibles innecesarios.

---

# Logging

Registrar:

* errores;
* excepciones;
* acciones críticas.

No registrar:

* contraseñas;
* tokens;
* información privada.

---

# Convenciones

Variables

camelCase

Interfaces

PascalCase

Componentes

PascalCase

Constantes

UPPER_CASE

Archivos

kebab-case

---

# Componentes reutilizables

Siempre reutilizar antes de crear uno nuevo.

Si un componente supera unas 200 líneas, valorar dividirlo.

---

# Diseño

Mobile First.

Responsive.

Accesible.

Evitar pantallas con demasiada información.

---

# Performance

Evitar:

* consultas innecesarias;
* renders repetidos;
* componentes gigantes.

Priorizar simplicidad frente a optimización prematura.

---

# Testing

Las funciones críticas deben poder probarse de forma aislada.

Especialmente:

* algoritmo de matching;
* autorización;
* validaciones.

---

# Dependencias

Antes de instalar una nueva dependencia:

1. Verificar si ya existe una solución en el proyecto.
2. Valorar si realmente aporta valor.
3. Evitar dependencias innecesarias.

---

# Principios para Lovable

Antes de generar código:

* Revisar esta arquitectura.
* Respetar la separación de capas.
* No mezclar lógica de negocio con componentes React.
* No acceder a PostgreSQL desde el frontend.
* No duplicar funcionalidades.
* Mantener el código simple y legible.
* Priorizar claridad frente a complejidad.
