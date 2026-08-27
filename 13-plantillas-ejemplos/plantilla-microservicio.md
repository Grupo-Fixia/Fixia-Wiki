# 📦 Plantilla Estándar para Microservicios

## 1. Objetivo
Proveer una estructura base estandarizada para el desarrollo de nuevos microservicios en **Fixia**, asegurando coherencia arquitectónica tanto en servicios Java (Spring Boot) como Python (FastAPI).

---

## 2. Estructura de Directorios Estándar

### A. Proyecto Java (Spring Boot 3 + Java 21)
```text
fixia-<servicio>-service/
├── .github/workflows/          # Pipelines de CI/CD
├── docker/
│   └── Dockerfile              # Multi-stage Dockerfile
├── src/
│   ├── main/
│   │   ├── java/com/fixia/<servicio>/
│   │   │   ├── config/         # Seguridad, Swagger, Beans
│   │   │   ├── controller/     # Endpoints REST (DTOs)
│   │   │   ├── domain/         # Modelos de dominio y Entidades
│   │   │   ├── dto/            # Data Transfer Objects
│   │   │   ├── exception/      # Global Exception Handler
│   │   │   ├── repository/     # Interfaces Spring Data / JPA
│   │   │   └── service/        # Lógica de negocio (Interfaces e Impl)
│   │   └── resources/
│   │       ├── application.yml # Configuración base
│   │       └── db/migration/   # Scripts Flyway/Liquibase
│   └── test/                   # Pruebas unitarias e integración
├── pom.xml / build.gradle
└── README.md
```

### B. Proyecto Python (FastAPI + Python 3.12)
```text
fixia-<servicio>-service/
├── .github/workflows/
├── app/
│   ├── api/                    # Routers y endpoints
│   ├── core/                   # Configuración, seguridad y variables de entorno
│   ├── db/                     # Sesiones de base de datos y migraciones (Alembic)
│   ├── models/                 # Modelos SQLAlchemy / Pydantic
│   ├── schemas/                # Schemas de validación
│   ├── services/               # Lógica de negocio
│   └── main.py                 # Punto de entrada FastAPI
├── tests/
├── Dockerfile
├── requirements.txt / pyproject.toml
└── README.md
```

---

## 3. Ejemplo de Controller / Router con DTOs de Entrada y Salida

### Ejercicio en Java (Spring Boot):
```java
@RestController
@RequestMapping("/api/v1/orders")
@RequiredArgsConstructor
public class OrderController {

    private final OrderService orderService;

    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public ResponseEntity<OrderResponseDto> createOrder(@Valid @RequestBody CreateOrderRequestDto dto) {
        OrderResponseDto response = orderService.createOrder(dto);
        return ResponseEntity.status(HttpStatus.CREATED).body(response);
    }
}
```
