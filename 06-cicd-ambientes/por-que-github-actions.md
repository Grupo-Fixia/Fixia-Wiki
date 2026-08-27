# ⚙️ ¿Por qué elegimos GitHub Actions para CI/CD?

## 1. Contexto y Justificación Tecnológica

En el ecosistema de **Fixia**, la elección de la herramienta de Integración y Despliegue Continuo (CI/CD) es fundamental para mantener la velocidad de entrega, la seguridad y la simplicidad operativa. Evaluamos alternativas como Jenkins, GitLab CI y CircleCI, seleccionando **GitHub Actions** como el estándar oficial.

---

## 2. Ventajas Principales

### 🎯 Integración Nativa con el Repositorio
- **Sin fricción:** Al estar alojado el código en GitHub, la ejecución de workflows reacciona directamente a eventos nativos (`pull_request`, `push`, `release`, `issue_comment`) sin necesidad de configurar webhooks externos.
- **Trazabilidad en PRs:** Los resultados de las pruebas, calidad de código y linters se muestran directamente en la interfaz del PR mediante los *GitHub Checks API*.

### 📦 Ecosistema y Modulardad (GitHub Marketplace)
- Uso de acciones oficiales y mantenidas por la comunidad para herramientas de nuestro stack (Docker, Kubernetes, AWS, SonarQube, Setup Java/Python/Node).
- Reutilización de flujos de trabajo compartidos mediante **Reusable Workflows**, reduciendo la duplicación de configuraciones YAML entre microservicios.

### 🔒 Gobernanza y Gestión de Secretos
- Integración nativa con **GitHub Environments**, permitiendo definir reglas de aprobación manual, restricción por ramas y secretos específicos por entorno (Dev, QA, Prod).
- Soporte nativo para autenticación segura mediante **OIDC (OpenID Connect)** con proveedores de nube como AWS, eliminando credenciales estáticas.

### 💰 Eficiencia de Costos y Escalabilidad
- **Runners Administrados (Hosted):** Cero costo de mantenimiento de infraestructura para pipelines estándar.
- **Runners Auto-hospedados (Self-hosted):** Posibilidad de ejecutar agentes propios en nuestros clústeres K3s/Kubernetes para tareas pesadas o con requerimientos especiales de red interna.

---

## 3. Cuadro Comparativo de Alternativas

| Criterio | GitHub Actions | Jenkins | GitLab CI |
| :--- | :--- | :--- | :--- |
| **Mantenimiento de Infraestructura** | **Nulo** (o mínimo si es Self-hosted) | Alto (requiere servidor dedicado) | Nulo (SaaS) |
| **Configuración** | Declarativa en YAML (`.github/workflows`) | Groovy / Jenkinsfile complejo | Declarativa en YAML (`.gitlab-ci.yml`) |
| **Integración con GitHub** | **Nativa 100%** | Requiere Plugins / Webhooks | Compleja (Requiere mirror/sync) |
| **Curva de Aprendizaje** | Baja | Alta | Media |

---

## 4. Conclusión

GitHub Actions nos proporciona el balance óptimo entre **simplicidad, seguridad y potencia**, reduciendo drásticamente la sobrecarga operacional de DevOps y permitiendo al equipo enfocarse en entregar valor funcional con alta calidad.
