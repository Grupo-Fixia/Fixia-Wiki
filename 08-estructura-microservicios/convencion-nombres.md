# 🏷️ Convención de Nombres en el Ecosistema

## 1. Objetivo

Fijar los estándares de nomenclatura para repositorios, código, artefactos, endpoints y recursos de infraestructura en **Fixia** para asegurar consistencia e interoperabilidad.

---

## 2. Repositorios y Servicios

- **Repositorios de Microservicios:** `kebab-case` prefijado con `fixia-`.
  - *Ejemplos:* `fixia-auth-service`, `fixia-order-service`, `fixia-web-frontend`.
- **Imágenes de Docker:** `fixia/<nombre-microservicio>:<tag>`
  - *Ejemplo:* `fixia/fixia-order-service:1.2.0`

---

## 3. Código Fuente por Lenguaje

### Java (Spring Boot)
- **Paquetes:** `lowercase` sin guiones (`com.fixia.order.domain`)
- **Clases e Interfaces:** `PascalCase` (`OrderService`, `UserRepository`)
- **Métodos y Variables:** `camelCase` (`findOrderById`, `totalAmount`)
- **Constantes:** `UPPER_SNAKE_CASE` (`MAX_RETRY_ATTEMPTS`)

### Python (FastAPI / Django)
- **Módulos y Archivos:** `snake_case` (`order_repository.py`, `auth_utils.py`)
- **Clases:** `PascalCase` (`OrderResponseDTO`, `UserDomain`)
- **Funciones, Métodos y Variables:** `snake_case` (`calculate_tax`, `user_id`)
- **Constantes:** `UPPER_SNAKE_CASE` (`DEFAULT_PAGE_SIZE`)

---

## 4. API REST y Endpoints

- **Rutas (URIs):** `kebab-case`, en plural para recursos.
  - ✅ `/api/v1/services-orders`
  - ❌ `/api/v1/serviceOrder` o `/api/v1/get_orders`
- **Parámetros Query:** `camelCase` (`/api/v1/orders?userId=123&status=PENDING`)
- **Encabezados HTTP personalizados:** `X-Fixia-<Nombre>` (`X-Fixia-Trace-Id`)

---

## 5. Base de Datos (PostgreSQL)

- **Tablas:** `snake_case` en plural (`users`, `service_orders`, `payment_transactions`).
- **Columnas:** `snake_case` (`created_at`, `user_id`, `is_active`).
- **Claves Primarias:** `id` (preferiblemente `UUID` o `BIGINT`).
- **Claves Foráneas:** `<tabla_singular>_id` (`user_id`, `order_id`).
