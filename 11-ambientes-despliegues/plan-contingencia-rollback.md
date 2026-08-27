# 🔄 Plan de Contingencia y Rollback

## 1. Objetivo
Garantizar procedimientos inmediatos de recuperación ante fallos críticos o degradación de servicio detectada tras un nuevo despliegue en **Fixia**.

---

## 2. Umbrales de Activación de Rollback (Triggers)
El plan de rollback se ejecutará de forma automática o manual si se cumple cualquiera de las siguientes condiciones en los 15 minutos posteriores al despliegue:
- Error rate en peticiones HTTP superando el **2%** de las solicitudes totales.
- Incremento del percentil p95 de latencia por encima de **800ms**.
- Fallo recurrente en las pruebas de `livenessProbe` / `readinessProbe` con reinicios en bucle (*CrashLoopBackOff*).

---

## 3. Procedimiento Paso a Paso

### A. Rollback Automático de Aplicación (Kubernetes Native)
```bash
# Revertir deployment a la revisión anterior
kubectl rollout undo deployment/<service-name> -n fixia-prod

# Verificar estado del rollout
kubectl rollout status deployment/<service-name> -n fixia-prod
```

### B. Rollback de Migraciones de Base de Datos
- Las migraciones (Flyway/Liquibase para Java, Alembic para Python) **deben ser siempre retrocompatibles** (Zero-Downtime Database Migration Pattern).
- Si un despliegue requiere eliminar una columna, la fase de *Drop* debe ser postergada a la siguiente versión (*Expand-and-Contract Pattern*).

---

## 4. Matriz de Severidad e Incidentes

| Nivel | Definición | Tiempo Máximo de Respuesta (RTO) | Acción Principal |
| :---: | :--- | :---: | :--- |
| **SEV-1** | Caída total de la plataforma o servicio de pagos. | `< 15 min` | Rollback inmediato a última versión estable. |
| **SEV-2** | Fallo en una funcionalidad no crítica (ej. notificaciones). | `< 1 hora` | Aplicación de *Hotfix* en pipeline directo. |
| **SEV-3** | Degradación menor de rendimiento o error visual. | `< 24 horas` | Corrección programada en el siguiente sprint. |
