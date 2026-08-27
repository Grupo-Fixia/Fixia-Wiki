# 🚀 Módulo 11: Ambientes, Despliegues y Planes de Contingencia

## 📌 Descripción del Módulo

Este módulo define la estrategia global de entornos de ejecución, los patrones de despliegue continuo y las políticas de recuperación ante fallos (Rollback y Manejo de Incidentes) para la plataforma **Fixia**.

Se enfoca en garantizar la estabilidad del servicio, despliegues sin tiempo de inactividad (*Zero Downtime*) y paridad estricta entre desarrollo, pruebas y producción.

---

## 🗂️ Contenido de la Sección

| Archivo | Descripción |
| :--- | :--- |
| **[`estrategia-ambientes.md`](./estrategia-ambientes.md)** | Definición de los entornos DEV, STG y PROD, dominios, aislamiento de datos y paridad de ambientes. |
| **[`estrategia-despliegues.md`](./estrategia-despliegues.md)** | Estrategias de actualización en K3s (Rolling Updates, Blue/Green), ventanas de mantenimiento y automatización. |
| **[`plan-contingencia-rollback.md`](./plan-contingencia-rollback.md)** | Criterios para rollback inmediato, ejecución con `kubectl`, migraciones retrocompatibles y matriz de severidad. |

---

## 📋 Reglas de Oro para Despliegues

1. **Paridad de Configuración:** Nunca modificar configuraciones directamente en el cluster (`kubectl edit`); todo cambio debe pasar por Git.
2. **Backward Compatibility:** Toda modificación en bases de datos debe ser retrocompatible con la versión anterior del microservicio.
3. **Observabilidad Inmediata:** Monitorear dashboards de Grafana y logs centralizados durante los primeros 15 minutos post-despliegue.
