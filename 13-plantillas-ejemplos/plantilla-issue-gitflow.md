# 📋 Plantilla Estándar para Issues y Git Flow

## 1. Objetivo
Estandarizar la creación de historias de usuario, reportes de errores y convenciones de ramas/commits bajo la estrategia **Git Flow** en **Fixia**.

---

## 2. Plantilla de Bug / Feature (`.github/ISSUE_TEMPLATE/feature_request.md`)

```markdown
---
name: Feature Request / Tarea técnica
about: Sugerir una nueva característica o requerimiento técnico para Fixia.
title: '[FEAT] - '
labels: 'enhancement'
assignees: ''
---

## 🎯 Descripción del Requerimiento
<!-- Breve explicación del objetivo técnico o de negocio -->

## 📋 Criterios de Aceptación
- [ ] Dado que... Cuando... Entonces...
- [ ] Endpoint documentado en OpenAPI / Swagger.
- [ ] Pruebas de unidad con cobertura > 80%.

## 🏗️ Impacto Arquitectónico
- Microservicios implicados: `fixia-order-service`, `fixia-user-service`.
- Cambios en Base de Datos: [ ] Sí (Migración Flyway/Alembic requerida)  [ ] No
```

---

## 3. Convención de Commits y Ramas (Git Flow)

### Estrategia de Ramas:
- `main`: Código en producción.
- `develop`: Integración continua de funcionalidades.
- `feature/<issue-id>-<descripcion>`: Desarrollos específicos (ej: `feature/FIX-102-auth-jwt`).
- `hotfix/<version>`: Corrección crítica directa en producción.

### Formato de Commits (Conventional Commits):
- `feat(auth): agregar soporte para renovación de tokens JWT`
- `fix(order): corregir cálculo de impuestos en transacción`
- `docs(readme): actualizar guía de despliegue local`
- `refactor(db): optimizar consulta espacial en PostGIS`
