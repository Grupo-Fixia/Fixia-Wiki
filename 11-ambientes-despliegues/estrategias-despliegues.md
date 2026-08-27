# 🚢 Estrategias de Despliegue (Deployment Strategies)

## 1. Objetivo
Establecer los patrones de entrega de software para minimizar el tiempo de inactividad (*zero-downtime*) y reducir el impacto operacional ante releases de microservicios.

---

## 2. Patrones de Despliegue Adoptados

### A. Rolling Updates (Predeterminado para Microservicios)
Sustitución gradual de pods antiguos por instancias con la nueva versión de la imagen.

```yaml
# Configuración en Manifiesto K8s/K3s
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%        # Máximo de pods adicionales durante el despliegue
      maxUnavailable: 0%   # Garantiza cero tiempo de inactividad
```

### B. Blue/Green Deployments (Para Releases Mayores)
Mantiene dos entornos de producción idénticos (Azul y Verde). El tráfico del Ingress Ingress/Traefik se conmuta instantáneamente tras validar la salud del nuevo entorno.

```text
                  +-----------------------+
                  |  Traefik Ingress Router|
                  +-----------+-----------+
                              |
                     [ Switch Traffic ]
                              |
      +-----------------------+-----------------------+
      | (Active)                                      | (Idle / Testing)
+-----v-----------------+                       +-----v-----------------+
|   Blue Deployment     |                       |   Green Deployment    |
|   (v1.4.0 - Old)      |                       |   (v1.5.0 - New)      |
+-----------------------+                       +-----------------------+
```

---

## 3. Ventanas y Control de Despliegues
- Despliegues a **`PROD`** automatizados mediante GitHub Actions tras aprobación del equipo de Leads.
- Despliegues restringidos durante horarios pico de uso (12:00 PM - 2:00 PM y 6:00 PM - 9:00 PM UTC-5).
