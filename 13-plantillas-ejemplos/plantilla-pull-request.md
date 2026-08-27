# 🔀 Plantilla Estándar para Pull Requests (PRs)

## 1. Objetivo
Asegurar que todas las contribuciones de código en el ecosistema **Fixia** cumplan con las revisiones de pares, trazabilidad de requerimientos y estándares de calidad antes de integrarse a la rama principal.

---

## 2. Plantilla Markdown (`.github/PULL_REQUEST_TEMPLATE.md`)

```markdown
## 📌 Descripción del Cambio
<!-- Resume de forma clara los cambios introducidos en este PR -->

## 🔗 Incidencia / Tarea Asociada
- Closes #<!-- Número de Issue / Tarea -->

## 🧪 Pruebas Realizadas
- [ ] Pruebas unitarias ejecutadas exitosamente (`mvn test` / `pytest`).
- [ ] Pruebas de integración locales completadas.
- [ ] Validación manual en ambiente local / dev.

## 🛡️ Lista de Chequeo de Código (Checklist)
- [ ] El código sigue los estándares del proyecto (formato, nombres, paquetes).
- [ ] Se incluyeron pruebas unitarias para la nueva funcionalidad o corrección.
- [ ] No existen datos sensibles, credenciales ni tokens hardcodeados.
- [ ] Se actualizaron los manifiestos / variables de entorno si aplica.
- [ ] Los endpoints creados o modificados cuentan con sus DTOs documentados.

## 📸 Capturas de Pantalla / Evidencia (Opcional)
<!-- Adjuntar capturas de logs, llamadas Postman o resultados de pruebas si aplica -->
