# TDD (Test-Driven Development)

**Estado en el Tech Radar:** 🟢 Adoptar
**Categoría:** Técnicas & Métodos
**Última revisión:** 2026-08-24

## Contexto

Fixia exige una cobertura mínima del 85% como parte del Quality Gate (ver
[07-calidad-arquitectura](../../07-calidad-arquitectura/) y
[herramientas/sonarqube.md](../herramientas/sonarqube.md)). Alcanzar ese
umbral escribiendo tests **después** del código tiende a producir tests
superficiales que solo persiguen el número de cobertura, no que validan
comportamiento real.

## Decisión

TDD (escribir el test antes que el código de producción) es la práctica
recomendada por defecto para lógica de negocio en `domain/` y
`application/`, dentro de la estructura de Clean Architecture (ver
[clean-architecture.md](./clean-architecture.md)).

## Alternativas consideradas

| Opción | Por qué no como default |
|---|---|
| **Tests después del código (test-after)** | Tiende a producir tests que confirman lo que el código ya hace, no que definen el comportamiento esperado; más fácil que terminen siendo superficiales solo para cumplir el umbral de cobertura. |
| **Sin disciplina formal de testing, solo cobertura mínima** | El Quality Gate mide cobertura, no calidad de los tests; sin una práctica que guíe cómo escribirlos, un equipo bajo presión de tiempo puede escribir tests triviales que inflan el número sin validar nada útil. |

## Justificación para Fixia

1. **Clean Architecture ya deja la lógica de negocio testeable de forma
   aislada** (casos de uso que dependen de interfaces, no de
   implementaciones concretas — ver
   [clean-architecture.md](./clean-architecture.md)). TDD aprovecha
   directamente esa estructura: se puede escribir un test de un caso de
   uso con un repositorio mockeado antes de implementar nada de
   infraestructura real.
2. **Dominios con reglas de negocio no triviales se benefician
   especialmente.** Solicitudes (máquina de estados: creada → aceptada →
   en curso → completada/cancelada) y Pagos son los casos donde escribir
   el test primero obliga a pensar explícitamente los casos borde (¿qué
   pasa si se intenta aceptar una solicitud ya aceptada?) antes de que el
   código exista.
3. **Reduce el costo de refactor a futuro.** Con microservicios que van a
   evolucionar (ej. Búsqueda y Matching incorporando nuevas señales de
   ranking), tener una suite de tests que valida comportamiento, no
   implementación, permite refactorizar con confianza.

## Cómo se usa en el proyecto

- Cobertura mínima exigida por el Quality Gate: 85% del código nuevo (ver
  [07-calidad-arquitectura](../../07-calidad-arquitectura/)).
- Tests unitarios se ejecutan en cada push y PR; tests de integración en
  PR hacia `main`/`test`; tests E2E antes de release a producción (ver
  tabla completa en
  [04-gobierno-ciclo-vida](../../04-gobierno-ciclo-vida/)).
- Estructura de carpetas de tests dentro de cada microservicio:
  `tests/unit/` y `tests/integration/`.
- Los casos de uso (`application/`) se testean con mocks de los
  repositorios (puertos), sin depender de una base de datos real
  levantada.

## Trade-offs / riesgos

- Curva de adopción para desarrolladores sin experiencia previa en TDD;
  al principio puede sentirse más lento que escribir código directo.
- TDD estricto en cada línea de infraestructura (`infrastructure/`) no
  siempre aporta el mismo valor que en `domain/`/`application/`; se
  prioriza la disciplina donde vive la lógica de negocio real.

## Cuándo reconsiderar

No se reconsidera como práctica recomendada de fondo. Se puede flexibilizar
puntualmente para código de infraestructura muy delgado (ej. un wrapper
trivial de un cliente HTTP) donde el valor de escribir el test primero es
marginal — mientras la cobertura total del microservicio siga cumpliendo
el Quality Gate.
