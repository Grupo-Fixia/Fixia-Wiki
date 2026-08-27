# Event-Driven

**Estado en el Tech Radar:** 🟡 Probar
**Categoría:** Técnicas & Métodos
**Última revisión:** 2026-08-24

## Contexto

No toda comunicación entre microservicios de Fixia necesita respuesta
inmediata. Cuando una solicitud se acepta, el dominio de Notificaciones
necesita enterarse para avisar al cliente — pero Solicitudes no debería
bloquearse ni fallar si Notificaciones está temporalmente caído (ver
relaciones entre dominios en
[01-contexto-proyecto/mapa-dominios-negocio.md](../../01-contexto-proyecto/mapa-dominios-negocio.md)).

## Decisión

Se adopta un enfoque **event-driven** (basado en eventos, vía cola de
mensajería) para comunicación asíncrona entre microservicios, en estado
**Probar**, como complemento de la comunicación síncrona REST/gRPC para
consultas que sí requieren respuesta inmediata.

## Alternativas consideradas

| Opción | Por qué no |
|---|---|
| **Todo síncrono (REST/gRPC) incluso para notificaciones** | Acopla la disponibilidad de Solicitudes a la disponibilidad de Notificaciones; si Notificaciones falla o está lento, no debería impedir que una solicitud se acepte. |
| **Polling en vez de eventos** | Un microservicio consultando periódicamente a otro para ver si "algo cambió" agrega latencia y carga innecesaria comparado con que el emisor notifique el cambio de forma directa. |
| **Comunicación directa vía base de datos compartida** | Viola directamente el principio Database per Service (ver [10-seguridad-datos](../../10-seguridad-datos/)); un microservicio nunca debe leer la base de datos de otro. |

## Justificación para Fixia

1. **Desacopla el flujo principal de negocio de procesos secundarios.**
   El "camino feliz" (cliente pide → proveedor acepta → se completa → se
   paga → se reseña, orquestado por Solicitudes) no debe depender de que
   Notificaciones o Reseñas estén disponibles en ese instante exacto.
2. **Refuerza el aislamiento de fallos que motivó microservicios en
   primer lugar** (ver
   [01-contexto-proyecto/por-que-microservicios.md](../../01-contexto-proyecto/por-que-microservicios.md)):
   un dominio no crítico caído no debe tumbar uno crítico.
3. **Encaja naturalmente con los eventos ya identificados en el mapa de
   dominios**: `ubicacion_actualizada` (Proveedores → Geolocalización),
   `solicitud_aceptada` (Solicitudes → Notificaciones),
   `solicitud_completada` (Solicitudes → Reseñas).

## Por qué está en "Probar" y no en "Adoptar"

El equipo está validando la elección concreta de broker de mensajería
(RabbitMQ o Kafka) y el patrón de manejo de errores/reintentos en
consumidores antes de comprometerse como estándar en todos los dominios
que lo ameriten.

## Cómo se usa en el proyecto (mientras está en Probar)

- Comunicación asíncrona vía cola/broker de mensajería, con autenticación
  habilitada y canal cifrado (AMQPS o TLS, ver
  [10-seguridad-datos](../../10-seguridad-datos/)).
- Un microservicio emite un evento sin esperar respuesta ni bloquear su
  propio flujo si el consumidor está caído.
- Eventos identificados hoy en el flujo principal de Fixia:
  `ubicacion_actualizada`, `solicitud_aceptada`, `solicitud_completada`.
- La comunicación síncrona (REST/gRPC) se mantiene para consultas que sí
  requieren respuesta inmediata (ej. Búsqueda consultando disponibilidad
  de un proveedor en tiempo real).

## Trade-offs / riesgos

- **Consistencia eventual**: el consumidor de un evento puede procesarlo
  con un pequeño retraso; se debe diseñar explícitamente qué operaciones
  toleran esto (notificaciones, sí) y cuáles no (pagos, requieren
  confirmación síncrona).
- Complejidad operativa adicional: un broker de mensajería es un
  componente más que monitorear y mantener disponible.
- Requiere diseño cuidadoso de reintentos e idempotencia en los
  consumidores, para evitar procesar el mismo evento dos veces (ej. no
  enviar la misma notificación duplicada).

## Cuándo reconsiderar

Pasa a **Adoptar** cuando se confirme el broker de mensajería definitivo
y su configuración de seguridad/monitoreo esté estable en al menos dos
flujos de eventos reales en producción.
