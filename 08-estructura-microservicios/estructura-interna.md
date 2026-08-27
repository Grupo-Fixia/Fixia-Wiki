# 📁 Estructura Interna Estándar de Microservicios

## 1. Objetivo

Definir la disposición estandarizada de archivos y carpetas para los microservicios de **Fixia**, independientemente del lenguaje o marco de trabajo utilizado.

---

## 2. Estructura Estándar para Microservicios Java (Spring Boot)

```text
fixia-order-service/
├── .github/
│   └── workflows/          # CI/CD Workflows
├── docker/                 # Dockerfile y scripts de despliegue
├── src/
│   ├── main/
│   │   ├── java/com/fixia/order/
│   │   │   ├── domain/     # Entidades, Value Objects, Excepciones
│   │   │   ├── application/# Casos de Uso, DTOs, Puertos
│   │   │   └── infrastructure/
│   │   │       ├── adapters/    # Repositorios JPA, Clientes REST/Kafka
│   │   │       ├── config/      # Beans de Spring, Seguridad
│   │   │       └── entrypoints/ # Controllers REST
│   │   └── resources/
│   │       ├── application.yml
│   │       └── db/migration/    # Scripts Flyway/Liquibase
│   └── test/               # Pruebas Unitarias e Integración
├── pom.xml / build.gradle
└── README.md
```

---

## 3. Estructura Estándar para Microservicios Python (FastAPI)

```text
fixia-user-service/
├── .github/
│   └── workflows/
├── app/
│   ├── core/               # Configuración global, seguridad, settings
│   ├── domain/             # Entidades y reglas de negocio puras
│   ├── application/        # Casos de uso y esquemas Pydantic
│   └── infrastructure/
│       ├── db/             # Modelos SQLAlchemy, sesiones, migraciones Alembic
│       ├── adapters/       # Servicios externos, clientes HTTP
│       └── api/            # Routers de FastAPI (Endpoints)
├── tests/                  # Test suite (pytest)
├── Dockerfile
├── requirements.txt / pyproject.toml
└── README.md
```

---

## 4. Archivos Obligatorios en el Raíz

Todo repositorio de microservicio en Fixia debe incluir en la raíz:
- `README.md`: Descripción del servicio, variables de entorno requeridas y comandos para ejecución local.
- `Dockerfile`: Configuración de compilación multietapa (*Multi-stage build*).
- `.gitignore`: Configuración para evitar subir código compilado, secretos o archivos de IDE.
- `.env.example`: Plantilla de variables de entorno de desarrollo local sin valores reales.y


