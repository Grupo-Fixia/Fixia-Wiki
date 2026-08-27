# 🧩 Catálogo de Microservicios de Fixia

## 1. Objetivo

Documentar el ecosistema de microservicios de **Fixia**, especificando el propósito de cada componente, su tecnología base, responsabilidades y comunicación en la arquitectura distribuida.

---

## 2. Mapa del Ecosistema

```text
               +----------------------------------+
               |        API Gateway (Kong / NGINX)|
               +----------------+-----------------+
                                |
     +--------------------------+--------------------------+
     |                          |                          |
+----v---------------+  +-------v------------+  +----------v---------+
| fixia-auth-service |  | fixia-user-service |  | fixia-order-service|
| (Spring Boot / JWT)|  | (FastAPI / Postgres|  | (Spring Boot / JPA)|
+--------------------+  +--------------------+  +----------+---------+
                                                           |
                                                +----------v---------+
                                                | fixia-payment-svc  |
                                                | (FastAPI / Stripe) |
                                                +--------------------+
```

---

## 3. Registro de Servicios

| Microservicio | Tecnología Base | BD / Almacenamiento | Puerto Local | Responsabilidad Principal |
| :--- | :--- | :--- | :---: | :--- |
| **`fixia-auth-service`** | Java 21 (Spring Boot) | Redis / PostgreSQL | `8081` | Autenticación, gestión de tokens JWT, roles y permisos. |
| **`fixia-user-service`** | Python 3.12 (FastAPI) | PostgreSQL + PostGIS | `8082` | Gestión de usuarios, perfiles de clientes y profesionales, geolocalización. |
| **`fixia-order-service`** | Java 21 (Spring Boot) | PostgreSQL | `8083` | Gestión del ciclo de vida de solicitudes de servicio, asignación y estados. |
| **`fixia-payment-service`** | Python 3.12 (FastAPI) | PostgreSQL / MongoDB | `8084` | Procesamiento de pagos, pasarelas externas (Stripe/PayU) y facturación. |
| **`fixia-notification-svc`** | Node.js / NestJS | MongoDB / Redis | `8085` | Envío de notificaciones Push, SMS (Twilio) y correos (SendGrid). |
| **`fixia-review-service`** | Python 3.12 (FastAPI) | MongoDB | `8086` | Sistema de calificaciones, reseñas y reputación de profesionales. |

---

## 4. Comunicación Inter-servicio

1. **Síncrona (REST / gRPC):** Utilizada exclusivamente para consultas puntuales de baja latencia vía API Gateway o comunicación interna validada.
2. **Asíncrona (Apache Kafka):** Eventos de dominio para desacoplar flujos (ej. `orden-creada` ➡️ `fixia-notification-svc` / `fixia-payment-svc`).
