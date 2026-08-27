# CQRS (Command Query Responsibility Segregation)

**Estado en el Tech Radar:** 🟠 Evaluar
**Categoría:** Técnicas & Métodos
**Última revisión:** 2026-08-24

## Contexto

El dominio de **Búsqueda y Matching** (ver
[01-contexto-proyecto/mapa-dominios-negocio.md](../../01-contexto-proyecto/mapa-dominios-negocio.md))
tiene un patrón de acceso muy asimétrico: se lee constantemente (cada
búsqueda de un cliente) pero se escribe con mucha menos frecuencia
relativa (cuando cambia la disponibilidad o el perfil de un proveedor). Un
mismo modelo de datos optimizado para ambos casos puede no ser óptimo
para ninguno.

## Decisión

CQRS queda en estado **Evaluar**: se considera como opción específica
para el dominio de Búsqueda y Matching, no como patrón general a aplicar
en todos los microservicios.

## Alternativas consideradas

| Opción | Por qué no (o por qué CQRS igual se evalúa) |
|---|---|
| **Un único modelo de lectura/escritura (sin CQRS)** | Es el enfoque por defecto en el resto de los dominios (Usuarios, Proveedores, Reseñas), donde la asimetría lectura/escritura no es tan marcada; para esos casos, CQRS agregaría complejidad sin beneficio claro. |
| **Solo agregar cache de lectura sin separar modelos (ej. Redis delante del modelo de escritura)** | Resuelve parte del problema de rendimiento de lectura con mucho menos costo de complejidad que CQRS completo; se evalúa como alternativa más simple antes de comprometerse con CQRS. |

## Justificación para evaluarlo en Búsqueda y Matching específicamente

1. **La asimetría lectura/escritura es real y medible.** Miles de
   búsquedas por cada actualización de disponibilidad de un proveedor
   es exactamente el escenario donde CQRS suele justificarse: un modelo
   de lectura desnormalizado y optimizado para consultas rápidas por
   proximidad, alimentado de forma asíncrona por los cambios del modelo
   de escritura.
2. **Se combina naturalmente con Event-Driven** (ver
   [event-driven.md](./event-driven.md)): el evento
   `ubicacion_actualizada` ya identificado en el mapa de dominios es
   justo el tipo de señal que actualizaría el modelo de lectura de
   Búsqueda sin acoplarlo síncronamente al dominio de Proveedores o
   Geolocalización.

## Por qué está en "Evaluar" y no en "Probar" todavía

A diferencia de DDD o Event-Driven (ya en Probar, con aplicación parcial
concreta), CQRS todavía no se implementó ni siquiera en un spike. Antes de
subir de anillo, se necesita:

- Confirmar que el volumen real de búsquedas justifica la complejidad
  adicional (medido, no asumido).
- Validar Event-Driven de forma estable primero (ver
  [event-driven.md](./event-driven.md)), ya que CQRS en Fixia dependería
  de esa infraestructura para sincronizar el modelo de lectura.

## Cómo se usaría en el proyecto (si se confirma tras evaluación)

- Aplicaría **únicamente** al dominio de Búsqueda y Matching, no como
  patrón general.
- El modelo de escritura seguiría viviendo en su base de datos
  transaccional habitual; el modelo de lectura sería una proyección
  desnormalizada optimizada para consultas geoespaciales, actualizada de
  forma asíncrona vía eventos.
- Cualquier otro dominio que en el futuro muestre una asimetría similar
  (poco probable hoy) debería documentar su propia justificación antes
  de aplicar CQRS, no asumir que aplica por defecto.

## Trade-offs / riesgos

- Complejidad significativa: dos modelos de datos a mantener sincronizados
  en vez de uno.
- Consistencia eventual entre el modelo de escritura y el de lectura — un
  cambio de disponibilidad de un proveedor podría tardar un instante en
  reflejarse en los resultados de búsqueda.
- Riesgo de sobre-ingeniería si se aplica sin la evidencia de volumen que
  lo justifique.

## Cuándo reconsiderar / avanzar

Pasa a **Probar** solo con datos concretos de volumen de búsquedas vs.
escrituras que demuestren el beneficio esperado, y con Event-Driven ya
estable como prerequisito técnico.
