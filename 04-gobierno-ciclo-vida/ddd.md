# DDD (Domain-Driven Design)

**Estado en el Tech Radar:** 🟡 Probar
**Categoría:** Técnicas & Métodos
**Última revisión:** 2026-08-24

## Contexto

Antes de nombrar un solo microservicio, Fixia necesitaba identificar los
límites de negocio reales (¿dónde termina "Proveedores" y empieza
"Geolocalización"?). Este ejercicio ya se hizo, de forma aplicada, en
[01-contexto-proyecto/mapa-dominios-negocio.md](../../01-contexto-proyecto/mapa-dominios-negocio.md).
Esta página documenta la técnica detrás de ese ejercicio, para que se
pueda repetir de forma consistente cuando aparezcan dominios nuevos.

## Decisión

Se adoptan conceptos de DDD —principalmente **bounded context** y
**lenguaje ubicuo**— en estado **Probar**: se usan como herramienta de
diseño al definir un dominio nuevo, sin adoptar el conjunto completo y más
pesado de DDD táctico (agregados complejos, event sourcing, etc.) todavía.

## Alternativas consideradas

| Opción | Por qué no |
|---|---|
| **Diseño de dominios ad-hoc, sin metodología** | Es, de hecho, lo que se evitó al construir el mapa de dominios inicial: sin un criterio explícito (bounded context), es fácil terminar con microservicios divididos por capa técnica en vez de por capacidad de negocio real (ver la justificación de separar Geolocalización de Proveedores en [01-contexto-proyecto](../../01-contexto-proyecto/mapa-dominios-negocio.md)). |
| **DDD completo (táctico + estratégico) desde el inicio** | El DDD táctico completo (agregados, value objects estrictos, event sourcing) agrega complejidad que no todos los dominios de Fixia justifican hoy (ej. Reseñas es un dominio simple). Se adopta lo estratégico (bounded contexts) primero, y lo táctico se evalúa dominio por dominio. |

## Justificación para Fixia

1. **El "lenguaje ubicuo" evita ambigüedad entre negocio y código.**
   Términos como "Solicitud", "Proveedor" o "Zona de cobertura" deben
   significar lo mismo en una conversación con el Product Owner, en un
   ticket de Jira, y en el nombre de una clase de dominio. Esto reduce
   fricción de comunicación en un equipo que crece.
2. **Bounded context es la herramienta que ya usamos para decidir los
   límites de cada microservicio.** No es una técnica nueva a introducir:
   es formalizar el criterio que ya se aplicó al separar, por ejemplo,
   Búsqueda/Matching de Proveedores (ver justificación completa en
   [01-contexto-proyecto/mapa-dominios-negocio.md](../../01-contexto-proyecto/mapa-dominios-negocio.md)).
3. **Evita el anti-patrón de "entidad anémica" compartida entre
   dominios.** Sin bounded contexts claros, es tentador reutilizar una
   misma entidad "Usuario" idéntica en todos los microservicios; DDD
   ayuda a reconocer que "Usuario" desde la perspectiva de Pagos
   (¿tiene un método de pago válido?) es distinto de "Usuario" desde la
   perspectiva de Reseñas (¿puede calificar este servicio?).

## Por qué está en "Probar" y no en "Adoptar"

Se aplica activamente en el diseño de dominios (mapa de dominios,
nombrado de microservicios), pero el equipo todavía no adopta las
herramientas tácticas más avanzadas de DDD (event storming formal,
agregados con invariantes complejas) de forma sistemática. Se sube a
"Adoptar" cuando eso se vuelva parte habitual del proceso de diseño de un
dominio nuevo.

## Cómo se usa en el proyecto (mientras está en Probar)

- Todo dominio nuevo (antes de convertirse en microservicio) pasa por una
  identificación explícita de su bounded context y su relación con
  dominios vecinos, siguiendo el mismo criterio aplicado en
  [01-contexto-proyecto/mapa-dominios-negocio.md](../../01-contexto-proyecto/mapa-dominios-negocio.md).
- El nombre de las entidades de dominio (`domain/entities/`) debe usar el
  mismo término que usa el negocio (Product Owner, tickets de Jira), no
  una traducción técnica distinta.

## Trade-offs / riesgos

- Sin experiencia previa del equipo en DDD, existe el riesgo de aplicarlo
  de forma superficial (solo nombrar carpetas "domain") sin el
  pensamiento real de bounded context detrás.

## Cuándo reconsiderar

Pasa a **Adoptar** cuando el equipo incorpore DDD táctico de forma
consistente en el diseño de nuevos dominios complejos (ej. si Pagos
requiere modelar agregados con invariantes estrictas de forma explícita).
