# 🌐 Estrategia y Configuración de Ambientes

## 1. Objetivo
Definir la separación estricta de entornos de ejecución para **Fixia**, garantizando aislamiento de datos, estabilidad operativa y paridad de configuración entre desarrollo y producción.

---

## 2. Matriz de Ambientes

| Ambiente | Propósito | Infraestructura | Dominio Base | Política de Datos |
| :--- | :--- | :--- | :--- | :--- |
| **`Development` (DEV)** | Desarrollo activo y pruebas unitarias/locales. | Docker Compose / Local K3d | `*.dev.fixia.internal` | Datos sintéticos (seeders). |
| **`Staging` (STG)** | Pruebas de integración, QA y pre-release. | Clúster K3s (Namespace `fixia-stg`) | `stg.fixia.com` | Datos anonimizados de producción. |
| **`Production` (PROD)**| Entorno vivo para usuarios finales. | Clúster K3s HA (Namespace `fixia-prod`) | `fixia.com` / `api.fixia.com` | Datos reales con respaldo contínuo. |

---

## 3. Paridad de Ambientes (Twelve-Factor App)
- **Configuración por Variables de Entorno:** Ninguna propiedad de entorno estará empaquetada dentro de las imágenes de Docker.
- **Servicios de Respaldo (Backing Services):** Las bases de datos y brokers (PostgreSQL, Redis, Kafka) se tratan como recursos adjuntos mediante abstracción de variables de conexión (`DATABASE_URL`, `KAFKA_BROKERS`).
