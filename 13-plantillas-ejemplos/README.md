# 📂 Módulo 13: Plantillas y Ejemplos de Referencia

## 📌 Descripción del Módulo

Este módulo reúne la colección de plantillas estándar, archivos base y ejemplos prácticos reutilizables para el desarrollo de software, despliegue de infraestructura y procesos de colaboración en **Fixia**.

El objetivo es acelerar la incorporación de nuevos microservicios y garantizar la consistencia en el código, manifiestos de Kubernetes y gestión del repositorio.

---

## 🗂️ Contenido de la Sección

| Archivo | Descripción |
| :--- | :--- |
| **[`plantilla-microservicio.md`](./plantilla-microservicio.md)** | Estructura base de directorios para Spring Boot (Java 21) y FastAPI (Python 3.12), junto con ejemplos de controladores. |
| **[`plantilla-manifiesto-k8s.md`](./plantilla-manifiesto-k8s.md)** | Plantilla YAML para `Deployment` y `Service` en K3s con probes de salud, límites de recursos y secretos. |
| **[`plantilla-pull-request.md`](./plantilla-pull-request.md)** | Estructura estándar para PRs en GitHub (`PULL_REQUEST_TEMPLATE.md`) con listas de chequeo y trazabilidad. |

---

## 💡 Modo de Uso

1. Copiar la estructura de directorios recomendada al crear un nuevo repositorio dentro de la organización.
2. Utilizar el manifiesto base de K8s sustituyendo los valores de contexto y variables.
3. Asegurar que el archivo de PR esté ubicado en `.github/PULL_REQUEST_TEMPLATE.md` en cada proyecto.
