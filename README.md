# DevQuest Infrastructure Setup

This repository contains Kubernetes deployment configurations and the necessary steps to set up the infrastructure for the DevQuest platform.

---

## Prerequisites

Ensure you have the following tools installed:

- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Helm](https://helm.sh/docs/intro/install/)
- [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)
- A running Kubernetes cluster (e.g., AKS)

---

## Repository Structure

```
.
├── kubernetes.yaml         # Deployment and Service configurations
├── config-map.yaml         # Configuration map for environment variables
├── README.md               # Project setup and instructions
```

---

## Steps to Set Up the Cluster

### 1. Apply Kubernetes Configuration

Apply the Kubernetes deployments and services:

```bash
kubectl apply -f config-map.yaml
kubectl apply -f kubernetes.yaml
```

Verify the deployments and services:

```bash
kubectl get all -n default
```

### 2. Expose Services via Ingress

Ensure you have an NGINX ingress controller installed:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```

Apply the ingress resource:

```bash
kubectl apply -f kubernetes.yaml
```

Verify the ingress:

```bash
kubectl get ingress -n default
```

---

## Prometheus and Grafana Integration

Follow these steps to add Prometheus and Grafana monitoring to your cluster.

### 1. Add Prometheus Helm Repository

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

### 2. Install Prometheus and Grafana

```bash
helm install prometheus prometheus-community/kube-prometheus-stack -n monitoring --create-namespace
```

### 3. Verify Installation

Check the running pods in the `monitoring` namespace:

```bash
kubectl get pods -n monitoring
```

### 4. Access Prometheus and Grafana

- **Prometheus**: 
  Forward the Prometheus port to localhost:
  ```bash
  kubectl port-forward -n monitoring svc/prometheus-kube-prometheus-prometheus 9090:9090
  ```
  Access it at [http://localhost:9090](http://localhost:9090).

- **Grafana**:
  Forward the Grafana port to localhost:
  ```bash
  kubectl port-forward -n monitoring svc/prometheus-grafana 3000:3000
  ```
  Access it at [http://localhost:3000](http://localhost:3000). 
  Use the default credentials:
  - Username: `admin`
  - Password: `prom-operator`

---

## Notes

1. Replace sensitive values like `MONGODB_URI` and `DB_NAME` in `config-map.yaml` with your actual configurations.
2. Ensure your domain or external IP is configured properly to access the services exposed through Ingress.
3. Customize resource requests and limits in `kubernetes.yaml` for production environments.

---

## Troubleshooting

- Check pod logs:
  ```bash
  kubectl logs <pod-name>
  ```
- Verify service connectivity:
  ```bash
  kubectl exec -it <pod-name> -- curl <service-name>
  ```
- Inspect ingress rules:
  ```bash
  kubectl describe ingress devquest-ingress
  
