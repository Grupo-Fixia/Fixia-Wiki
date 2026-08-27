# ☸️ Plantilla Estándar de Manifiesto Kubernetes (K3s)

## 1. Objetivo
Proporcionar una plantilla YAML unificada y validada para el despliegue de microservicios en el clúster K3s de **Fixia**, incorporando límites de recursos, probes de salud y variables inyectadas.

---

## 2. Manifiesto Completo (`deployment-service.yaml`)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fixia-example-service
  namespace: fixia-prod
  labels:
    app.kubernetes.io/name: fixia-example-service
    app.kubernetes.io/part-of: fixia-platform
spec:
  replicas: 2
  selector:
    matchLabels:
      app: fixia-example-service
  template:
    metadata:
      labels:
        app: fixia-example-service
    spec:
      containers:
      - name: example-service
        image: fixia/fixia-example-service:1.0.0
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: SPRING_PROFILES_ACTIVE
          value: "prod"
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: fixia-db-secrets
              key: db-password
        resources:
          requests:
            memory: "256Mi"
            cpu: "200m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /actuator/health/liveness
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /actuator/health/readiness
            port: 8080
          initialDelaySeconds: 15
          periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: fixia-example-service
  namespace: fixia-prod
spec:
  type: ClusterIP
  ports:
  - port: 80
    targetPort: 8080
    protocol: TCP
    name: http
  selector:
    app: fixia-example-service
```
