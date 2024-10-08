
# Kubernetes Monitoring with Prometheus and Grafana

Monitoring Kubernetes clusters is essential for maintaining visibility into your infrastructure and quickly identifying issues. This guide will walk you through setting up a monitoring platform using **Prometheus** and **Grafana** on a Kubernetes cluster.

## Introduction

Prometheus is an open-source monitoring platform that collects metrics in real-time, stores them in a time-series database (TSDB), and allows for powerful querying. While Prometheus provides a lot of information, it presents data in JSON format, which can be challenging to visualize. This is where **Grafana** comes in—Grafana can use Prometheus as a data source to create rich visualizations and dashboards.

### Prometheus Architecture

When you install Prometheus, it includes a component called the **Prometheus Server**, which scrapes metrics from various endpoints (e.g., `api/metrics` in Kubernetes). The data is stored in a time-series database (TSDB), and you can configure Prometheus to send alerts through an **Alertmanager**.

### Key Components:
- **Prometheus Server**: Collects and stores metrics.
- **TSDB**: Time-series database for storing metrics over time.
- **Alertmanager**: Sends alerts when specific conditions are met.

## Installation

### Installing Prometheus Using Helm

1. **Add Helm Repository**:
   ```bash
   helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
   ```

2. **Update Helm**:
   ```bash
   helm repo update
   ```

3. **Install Prometheus**:
   ```bash
   helm install prometheus prometheus-community/prometheus
   ```

### Exposing Prometheus Server

By default, Prometheus is installed as a **ClusterIP** service, meaning it's only accessible within the cluster. To make it accessible externally, convert it to a **NodePort** service:

```bash
kubectl expose service prometheus-server --type=NodePort --target-port=9090 --name=prometheus-server-ext
```

You can now access Prometheus using your Minikube IP and the NodePort:

```bash
minikube ip
# Use this IP with the exposed NodePort to access Prometheus
```

![image](https://github.com/user-attachments/assets/ce6b4f53-4de6-4503-89fe-d42c17f89a7b)

### Installing Grafana Using Helm

1. **Add Helm Repository**:
   ```bash
   helm repo add grafana https://grafana.github.io/helm-charts
   ```

2. **Update Helm**:
   ```bash
   helm repo update
   ```

3. **Install Grafana**:
   ```bash
   helm install grafana grafana/grafana
   ```

### Exposing Grafana

To access Grafana, you need to expose it similarly to Prometheus:

```bash
kubectl expose service grafana --type=NodePort --target-port=3000 --name=grafana-ext
```

Retrieve the Minikube IP and access Grafana using the appropriate port.

### Logging into Grafana

Retrieve the default Grafana admin password:

```bash
kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

The default username is `admin`. Use the retrieved password to log in.

![image](https://github.com/user-attachments/assets/568194ed-b538-42ee-a669-474e1e4af923)

## Connecting Grafana to Prometheus

1. **Add Prometheus as a Data Source**:
   - Go to **Data Sources** in Grafana.
   - Select **Prometheus**.
   - Provide the Prometheus server URL.
   - Click on **Save & Test**.

   If successful, a green popup will indicate the connection.

![image](https://github.com/user-attachments/assets/3226ed1b-80d4-4a71-8418-91a98f4c75b0)

2. **Importing a Kubernetes Dashboard**:
   - Click on **Dashboard** > **Import Dashboard**.
   - Enter the ID `3662` for the Kubernetes cluster dashboard.
   - Select **Prometheus** as the data source.
   - Click **Import** to view the dashboard.

This dashboard provides insights into your Kubernetes cluster using predefined Prometheus queries.

![image](https://github.com/user-attachments/assets/4b3b3355-c45c-4e18-b40b-0596a5cf1f25)


By following these steps, you’ve set up a powerful monitoring stack using Prometheus and Grafana. This setup will give you deep visibility into your Kubernetes clusters and help you quickly identify and resolve issues.

