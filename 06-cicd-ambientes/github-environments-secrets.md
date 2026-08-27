# 🔐 Configuración de Entornos y Secretos en GitHub (Environments & Secrets)

## 1. Objetivo

Definir los estándares para la gestión segura de variables de entorno, secretos y permisos dentro de GitHub Actions en los repositorios de **Fixia**, garantizando el principio de menor privilegio y la segregación de entornos.

---

## 2. Definición de Entornos (GitHub Environments)

Cada repositorio debe contar con los siguientes entornos configurados en GitHub (Settings > Environments):

| Entorno | Rama Asociada | Protección de Despliegue | Aprobadores Requeridos |
| :--- | :--- | :--- | :--- |
| **`development`** | `develop` | Ninguna (Despliegue automático tras CI exitoso) | 0 |
| **`staging` / `qa`** | `release/*` | Validación previa de pruebas de humo | 1 (Tech Lead / QA) |
| **`production`** | `main` | Revisión estricta y aprobación manual | 2 (DevOps Lead + Product Owner) |

---

## 3. Convención de Nombres para Secretos

Los secretos deben clasificarse según su alcance y nombrarse en `UPPER_SNAKE_CASE` con prefijos claros:

### Nivel Organización / Repositorio (Globales)
- `GH_PAT_READ_PACKAGES`: Personal Access Token para consumo de paquetes privados.
- `SONAR_TOKEN`: Token de autenticación para análisis en SonarQube/SonarCloud.
- `SNYK_TOKEN`: Token para escaneo de vulnerabilidades en dependencias.

### Nivel Entorno (Environment Secrets)
- `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY`: Credenciales de despliegue en AWS.
- `KUBECONFIG_DATA`: Certificado Base64 para conexión a clústeres K3s/Kubernetes.
- `DATABASE_URL`: Cadena de conexión a PostgreSQL/PostGIS.
- `JWT_SECRET_KEY`: Clave de firma de tokens JWT.

---

## 4. Buenas Prácticas de Seguridad

1. **Nunca harcodear credenciales:** Está estrictamente prohibido incluir llaves API, tokens o contraseñas en el código fuente.
2. **Uso de OIDC (OpenID Connect):** Para entornos de nube (AWS/GCP), priorizar el uso de roles asumiendo identidades OIDC en lugar de llaves estáticas de larga duración.
3. **Rotación periódica:** Todos los secretos de entornos staging y producción deben rotarse cada 90 días o inmediatamente ante cualquier sospecha de compromiso.
4. **Mascaramiento de logs:** Verificar que los valores de los secretos no sean impresos explícitamente en la consola durante los pasos de compilación o test.
