# Kitchen App

Production-ready student project for a cloud-native Kitchen Orders app.

## Stack
- API: Node.js + Express + MongoDB + Prometheus metrics
- UI: NGINX-served static page with same-origin API proxy
- Runtime: Docker Compose and Kubernetes
- Monitoring: Prometheus + Grafana
- CI: GitHub Actions

## Local (Docker Compose)
1. `docker compose up --build -d`
2. API health: `http://localhost:3000/health`
3. API NodePort equivalent in compose: `http://localhost:3000/orders`
4. UI: `http://localhost:8080`
5. Shutdown: `docker compose down`

## Kubernetes Apply Order
1. `kubectl apply -f k8s/namespace.yaml`
2. `kubectl create configmap api-config --from-literal=PORT=3000 --namespace kitchen-app --dry-run=client -o yaml | kubectl apply -f -`
3. `kubectl create configmap api-server-override --from-file=server.js=api/server.js --namespace kitchen-app --dry-run=client -o yaml | kubectl apply -f -`
4. `kubectl create secret generic mongodb-secret --from-literal=mongo-root-username=admin --from-literal=mongo-root-password=Secret123! --from-literal=mongo-uri='mongodb://admin:Secret123!@mongodb-service.kitchen-app.svc.cluster.local:27017/kitchendb?authSource=admin' --namespace kitchen-app --dry-run=client -o yaml | kubectl apply -f -`
5. `kubectl apply -f k8s/db/service.yaml`
6. `kubectl apply -f k8s/db/statefulset.yaml`
7. `kubectl apply -f k8s/api/deployment.yaml`
8. `kubectl apply -f k8s/web-ui/deployment.yaml`
9. `kubectl apply -f k8s/monitoring/prometheus-config.yaml`
10. `kubectl apply -f k8s/monitoring/grafana-dashboard.yaml`
11. `kubectl apply -f k8s/monitoring/grafana-deployment.yaml`

## Expected Kubernetes Services
- `api-nodeport` (30000)
- `api`
- `web-ui-service` (30080)
- `prometheus-service` (9090)
- `grafana-service` (3000)
- `mongodb-service`

## Quick Validation
- `kubectl get pods -n kitchen-app`
- `kubectl get svc -n kitchen-app`
- API health: `curl http://localhost:30000/health`
- Create order: `curl -X POST http://localhost:30000/orders -H "Content-Type: application/json" -d '{"dish":"Final Validation Meal"}'`
- UI: `http://localhost:30080`
- Grafana credentials: `admin / kitchenadmin`
-# KItchen-App