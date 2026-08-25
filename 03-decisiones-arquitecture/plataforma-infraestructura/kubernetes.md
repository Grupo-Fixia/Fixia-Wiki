# K3s / Kubernetes

**Estado en el Tech Radar:** 🟡 Probar
**Categoría:** Plataformas & Infraestructura
**Última revisión:** 2026-08-24

## Contexto

A medida que el número de microservicios de Fixia crece (ver
[08-estructura-microservicios/catalogo-microservicios-fixia.md](../../08-estructura-microservicios/catalogo-microservicios-fixia.md)),
gestionar contenedores individualmente con `docker-compose` deja de ser
suficiente para producción: hace falta balanceo de carga, autoescalado,
recuperación ante fallos y actualización sin downtime.

## Decisión

Se evalúa **K3s** (distribución liviana de Kubernetes) como capa de
orquestación para el entorno on-premise actual, con la mirada puesta en
una eventual migración a Kubernetes gestionado en nube (EKS/AKS/GKE).
Por ahora está en estado de **Probar**, no de adopción total.

## Alternativas consideradas

| Opción | Por qué no (todavía) |
|---|---|
| **Kubernetes completo (no K3s)** | Mayor complejidad operativa y de recursos de la que justifica el número actual de microservicios y el tamaño del equipo de DevOps. K3s ofrece el mismo API de Kubernetes con una huella mucho más liviana, ideal para on-premise. |
| **Docker Swarm** | Ecosistema y comunidad más pequeños; migrar después a Kubernetes gestionado en nube implicaría reescribir la orquestación, en vez de solo cambiar el proveedor del clúster. |
| **Nomad (HashiCorp)** | Válido técnicamente, pero fragmenta el ecosistema de herramientas (ya se usa o evalúa Terraform/Vault de HashiCorp para otras cosas, ver [terraform.md](./terraform.md)); K3s mantiene compatibilidad directa con el estándar de facto de la industria (Kubernetes), lo que facilita contratar talento y migrar a nube sin reescribir manifiestos. |

## Justificación para Fixia

1. **Mismo API que Kubernetes gestionado en nube.** Si Fixia migra a
   GCP/AWS/Azure a futuro, los manifiestos de K3s son compatibles con
   GKE/EKS/AKS sin reescritura significativa. Esto está directamente
   alineado con la estrategia de "portabilidad primero" que también
   justifica Docker y Terraform.
2. **Liviano para infraestructura on-premise limitada.** K3s está pensado
   para entornos con recursos acotados (edge, on-premise pequeño), que es
   exactamente el escenario actual de Fixia — a diferencia de Kubernetes
   completo, pensado para clústeres grandes.
3. **Escalado diferenciado por dominio.** El motivo original de elegir
   microservicios (ver
   [01-contexto-proyecto/por-que-microservicios.md](../../01-contexto-proyecto/por-que-microservicios.md))
   fue poder escalar Búsqueda/Geolocalización de forma independiente de,
   por ejemplo, Reseñas. K3s/Kubernetes es lo que hace eso operativamente
   posible (autoescalado por servicio), y no algo que `docker-compose`
   pueda resolver.

## Por qué está en "Probar" y no en "Adoptar"

Se está evaluando activamente en un subconjunto de microservicios antes
de comprometerse a nivel organizacional, para confirmar que:

- El equipo de DevOps puede operar y monitorear un clúster K3s de forma
  sostenida sin dedicación exclusiva.
- El número real de microservicios actuales justifica el overhead de
  orquestación frente a alternativas más simples (ej. contenedores
  gestionados directamente con Ansible + Docker).

## Cómo se usa en el proyecto (mientras está en Probar)

- Uso permitido en microservicios no críticos o en ambientes de test,
  documentando aprendizajes para el equipo.
- No se usa todavía como estrategia de despliegue de producción para
  dominios críticos como Pagos o Solicitudes.
- Cualquier manifiesto K3s se versiona en el repositorio de
  infraestructura (`fixia-infra-terraform` o equivalente, ver
  [08-estructura-microservicios](../../08-estructura-microservicios/)).

## Trade-offs / riesgos

- Curva de aprendizaje para el equipo si no tiene experiencia previa con
  Kubernetes.
- Un clúster mal configurado puede ser un punto único de falla si no se
  monitorea correctamente (ver
  [09-infraestructura-devops](../../09-infraestructura-devops/)).

## Cuándo reconsiderar

- Pasa a **Adoptar** cuando se valide en al menos 2-3 microservicios
  reales en producción con monitoreo estable por un ciclo completo de
  sprints.
- Se reconsideraría por completo si el número de microservicios se
  mantiene bajo y el overhead operativo no se justifica frente a un
  enfoque más simple.