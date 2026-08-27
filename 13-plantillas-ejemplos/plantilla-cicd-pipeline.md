# ⚙️ Plantilla de CI/CD (GitHub Actions Pipeline)

## 1. Objetivo
Estandarizar el pipeline de integración y despliegue continuo (CI/CD) para los microservicios de **Fixia**, ejecutando pruebas unitarias, análisis de código estático y construcción de imágenes Docker para su posterior despliegue en Kubernetes (K3s).

---

## 2. Pipeline Completo (`.github/workflows/ci-cd-pipeline.yml`)

```yaml
name: Fixia Microservice CI/CD Pipeline

on:
  push:
    branches: [ "main", "develop" ]
  pull_request:
    branches: [ "main", "develop" ]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  test-and-build-java:
    name: Build & Test (Spring Boot)
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up JDK 21
        uses: actions/setup-java@v4
        with:
          java-version: '21'
          distribution: 'temurin'
          cache: maven

      - name: Run Unit & Integration Tests
        run: mvn clean test

      - name: SonarQube Analysis
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
        run: |
          mvn sonar:sonar -Dsonar.projectKey=${{ github.event.repository.name }} \
                          -Dsonar.host.url=${{ secrets.SONAR_HOST_URL }}

  docker-build:
    name: Build & Push Docker Image
    needs: test-and-build-java
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Log in to Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Extract Metadata (Tags/Labels)
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
          tags: |
            type=sha,format=long
            type=raw,value=latest

      - name: Build and Push Docker Image
        uses: docker/build-push-action@v5
        with:
          context: .
          file: docker/Dockerfile
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
```
