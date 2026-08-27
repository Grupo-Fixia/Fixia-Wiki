# mTLS (Mutual TLS)

**Estado en el Tech Radar:** 🟠 Evaluar
**Categoría:** Técnicas & Métodos
**Última revisión:** 2026-08-24

## Contexto

La comunicación síncrona entre microservicios de Fixia hoy se autentica
con JWT firmado sobre TLS (ver
[10-seguridad-datos](../../10-seguridad-datos/)). Para los dominios más
sensibles — especialmente **Pagos**, y la comunicación que involucra
ubicación en tiempo real (**Geolocalización**) — vale la pena evaluar si
esa autenticación es suficiente o si conviene un mecanismo más fuerte.

## Decisión

mTLS queda en estado **Evaluar**: se considera para comunicación entre
microservicios de sensibilidad alta (Pagos, Geolocalización), no como
estándar general para toda comunicación interna todavía.

## Alternativas consideradas

| Opción | Por qué no (o por qué mTLS igual se evalúa) |
|---|---|
| **JWT firmado sobre TLS para todo (estado actual)** | Suficiente para la mayoría de la comunicación entre microservicios; se mantiene como estándar general. Se evalúa un mecanismo adicional solo donde el riesgo lo justifica. |
| **mTLS para toda comunicación interna sin excepción** | Añade complejidad operativa (gestión de certificados por servicio, rotación) en dominios donde el riesgo no lo justifica (ej. Reseñas); se prefiere aplicarlo selectivamente. |

## Justificación para evaluarlo en Pagos y Geolocalización específicamente

1. **Pagos maneja el dato más sensible del sistema** (ver
   [10-seguridad-datos](../../10-seguridad-datos/)): un JWT comprometido o
   mal validado en la comunicación hacia Pagos tiene el mayor impacto
   potencial de todo el sistema. mTLS agrega una capa de autenticación a
   nivel de transporte, no solo de aplicación.
2. **Geolocalización transmite ubicación en tiempo real de proveedores**,
   un dato sensible tanto por privacidad como por seguridad física (ver
   [01-contexto-proyecto/mapa-dominios-negocio.md](../../01-contexto-proyecto/mapa-dominios-negocio.md)).
   Asegurar que solo microservicios explícitamente autorizados puedan
   consumir ese canal reduce superficie de ataque.
3. **Ya está contemplado como opción en la política de seguridad
   general** de Fixia para "entornos que lo requieran por sensibilidad de
   datos" — esta página formaliza cuáles son esos entornos.

## Cómo se usaría en el proyecto (si se confirma tras evaluación)

- Aplicaría a la comunicación síncrona hacia/desde `fixia-msv-pagos` y
  `fixia-msv-geolocalizacion` en primer lugar.
- Requeriría gestión de certificados por servicio, con rotación
  automatizada — probablemente integrada con el gestor de secretos ya
  adoptado (HashiCorp Vault, ver
  [10-seguridad-datos](../../10-seguridad-datos/)).
- El resto de la comunicación entre microservicios seguiría usando JWT
  firmado sobre TLS como hoy, salvo que la evaluación demuestre que vale
  la pena extenderlo.

## Trade-offs / riesgos

- Complejidad operativa de gestión de certificados por servicio
  (emisión, rotación, revocación), que requiere tooling maduro para no
  convertirse en una fuente de incidentes por certificados vencidos.
- Curva de aprendizaje para el equipo si no tiene experiencia previa
  configurando mTLS en un entorno de microservicios.
- Sin K3s/Kubernetes consolidado (ver
  [plataformas-infraestructura/k3s-kubernetes.md](../plataformas-infraestructura/k3s-kubernetes.md)),
  implementar mTLS "a mano" es más trabajoso que con un service mesh que
  lo automatice; esta evaluación depende parcialmente de esa otra
  decisión.

## Cuándo reconsiderar / avanzar

Pasa a **Probar** con una prueba de concepto concreta en la comunicación
hacia `fixia-msv-pagos`, incluyendo el proceso real de gestión de
certificados, antes de considerar extenderlo a otros dominios sensibles.

