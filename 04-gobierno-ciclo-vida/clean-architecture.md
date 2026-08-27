# Clean Architecture

**Estado en el Tech Radar:** 🟢 Adoptar (obligatorio, sin excepción)
**Categoría:** Técnicas & Métodos
**Última revisión:** 2026-08-24

## Contexto

Fixia tiene, y va a seguir sumando, microservicios en más de un lenguaje
(Node.js/TypeScript, Python — ver
[lenguajes-frameworks/](../lenguajes-frameworks/)). Sin una estructura
interna común, cada microservicio terminaría organizado a criterio de
quien lo escribió, dificultando que cualquier desarrollador del equipo
pueda entender y mantener un microservicio que no construyó.

## Decisión

Todo microservicio de Fixia se organiza siguiendo las capas de Clean
Architecture (Robert C. Martin), con la regla de dependencia estricta: las
capas internas nunca dependen de las externas.

```
+-----------------------------------------+
|            Frameworks & Drivers          | ← DB, Web/HTTP, UI, mensajería
|  +-------------------------------------+  |
|  |         Interface Adapters          |  | ← Controllers, Gateways, Presenters
|  |  +---------------------------------+ |  |
|  |  |     Application / Use Cases     | |  | ← Lógica de aplicación
|  |  |  +-----------------------------+ | |  |
|  |  |  |     Entities (Dominio)      | | |  | ← Reglas de negocio puras
|  |  |  `------------------------------+ | |  |
|  |  `----------------------------------+ |  |
|  `--------------------------------------+  |
`------------------------------------------+
```

## Alternativas consideradas

| Opción | Por qué no |
|---|---|
| **Arquitectura por capas tradicional (MVC simple)** | Tiende a filtrar detalles de framework/DB hacia la lógica de negocio (ej. un modelo de Sequelize/TypeORM usado directamente como entidad de dominio), lo que dificulta testear reglas de negocio de forma aislada. |
| **Sin arquitectura formal (cada quien organiza a su criterio)** | Insostenible con múltiples microservicios y más de un lenguaje; cada repositorio se volvería incomparable con el resto, elevando el costo de onboarding y de mantenimiento cruzado entre equipos. |
| **Hexagonal Architecture (Ports & Adapters) como alternativa pura** | Conceptualmente muy cercana a Clean Architecture (de hecho, Clean Architecture es una evolución del mismo espíritu); se opta por Clean Architecture por ser más explícita en la separación de casos de uso, y porque ya está documentada con ejemplos concretos del equipo. |

## Justificación para Fixia

1. **Aislar la lógica de negocio de la infraestructura es crítico en un
   dominio como Solicitudes o Pagos**, donde una regla de negocio (ej.
   "una solicitud no puede aceptarse dos veces") no debe depender de si
   la persistencia es PostgreSQL o de si la API es REST o gRPC.
2. **Facilita el patrón Database per Service.** Con la capa
   `infrastructure/` aislada, cambiar el motor de base de datos de un
   microservicio (si algún dominio lo requiere) no toca la lógica de
   negocio (ver [10-seguridad-datos](../../10-seguridad-datos/)).
3. **Consistencia entre lenguajes.** Aunque un microservicio esté en
   Node.js y otro en Python (ver
   [lenguajes-frameworks/](../lenguajes-frameworks/)), ambos comparten la
   misma estructura de carpetas (`domain/`, `application/`,
   `infrastructure/`, `interfaces/`), lo que permite que un desarrollador
   entienda la organización de cualquier microservicio sin importar su
   lenguaje.
4. **Habilita TDD real.** Los casos de uso en `application/` dependen solo
   de interfaces (puertos), no de implementaciones concretas, lo que
   permite testearlos con mocks sin levantar una base de datos real (ver
   [tdd.md](./tdd.md)).

## Cómo se usa en el proyecto

- Estructura de carpetas obligatoria por microservicio:
  ```
  /src
    /domain          → Entities, reglas de negocio puras
    /application      → Use cases, interfaces de repositorios (puertos)
    /infrastructure    → Implementación de repositorios, DB, clientes HTTP
    /interfaces        → Controllers, DTOs, presenters (REST/gRPC)
  ```
- `domain/` no importa nada de `application/`; `application/` no importa
  nada de `infrastructure/` ni `interfaces/`. Las flechas de dependencia
  siempre apuntan hacia adentro.
- Patrón Repository obligatorio para abstraer acceso a datos, y
  Dependency Injection obligatoria para inyectar dependencias en los
  casos de uso (ver catálogo completo de patrones en
  [07-calidad-arquitectura](../../07-calidad-arquitectura/)).
- Todo PR que introduzca un microservicio nuevo debe documentar en su
  README qué patrones aplica y por qué (ver plantilla en
  [13-plantillas-ejemplos](../../13-plantillas-ejemplos/)).

## Trade-offs / riesgos

- Más archivos y niveles de indirección que un enfoque directo, lo que
  puede sentirse excesivo para un microservicio muy simple (ej. un CRUD
  mínimo). Se acepta el costo por la consistencia que aporta al conjunto.
- Requiere disciplina del equipo para no "atajar" importando
  infraestructura directamente en el dominio bajo presión de entrega —
  se refuerza parcialmente con reglas de linting de imports.

## Cuándo reconsiderar

No se reconsidera como estándar general. Un microservicio individual
podría justificar una estructura más simple solo si es deliberadamente
efímero (ej. un prototipo descartable), y debe documentarse como excepción
explícita, no como default silencioso.
