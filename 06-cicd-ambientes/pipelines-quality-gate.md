# 🛡️ Integración de Quality Gates en Pipelines de CI/CD

## 1. Objetivo

Establecer la configuración y las políticas automatizadas de **Quality Gates** en GitHub Actions y SonarQube para bloquear la integración de código que no cumpla con los estándares de calidad, seguridad y cobertura de **Fixia**.

---

## 2. Umbrales del Quality Gate (SonarQube / SonarCloud)

Para que una Pull Request pueda ser aprobada e integrada a `develop` o `main`, debe pasar el **Quality Gate** en SonarQube con los siguientes umbrales mínimos sobre el **Código Nuevo**:

| Métrica | Umbral Requerido | Acción en caso de fallo |
| :--- | :---: | :--- |
| **Cobertura de Código (Coverage)** | **≥ 80.0%** | Bloqueo de PR (Check fallido) |
| **Vulnerabilidades de Seguridad** | **0** (Cero) | Bloqueo inmediato |
| **Security Hotspots Revisados** | **100%** | Bloqueo de PR |
| **Bugs Críticos / Blocker** | **0** (Cero) | Bloqueo de PR |
| **Duplicación de Código** | **< 3.0%** | Advertencia / Bloqueo si supera 5% |
| **Deuda Técnica (Maintainability)** | **Rating A** | Advertencia |

---

## 3. Estructura del Job de Quality Gate en GitHub Actions

Ejemplo de cómo se integra la verificación del Quality Gate dentro del workflow de CI:

```yaml
name: CI & Quality Gate

on:
  pull_request:
    branches: [ develop, main ]

jobs:
  test-and-analyze:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout del código
        uses: actions/checkout@v4
        with:
          fetch-depth: 0 # Necesario para análisis de SonarQube

      - name: Configurar JDK 21
        uses: actions/setup-java@v4
        with:
          java-version: '21'
          distribution: 'temurin'

      - name: Ejecutar Tests y Cobertura (JaCoCo)
        run: ./mvnw clean verify

      - name: SonarQube Scan & Quality Gate Check
        uses: sonarsource/sonarqube-scan-action@v2
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
          SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}

      - name: Validar Estado del Quality Gate
        uses: sonarsource/sonarqube-quality-gate-action@v1
        timeout-minutes: 5
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
```

---

## 4. Política de Excepciones

Si un caso fortuito exige omitir temporalmente una regla del Quality Gate:
1. Se debe crear un ticket en Jira especificando el motivo técnico y el plan de remediación.
2. Requiere la aprobación explícita del **Tech Lead** y el **DevOps Lead**.
3. El plazo máximo de corrección no podrá exceder los **5 días hábiles**.
