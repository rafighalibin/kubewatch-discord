# Kubewatch Deployment (Helm)

This repository contains Helm values and manifests for deploying Kubewatch into an existing Kubernetes cluster with Discord notifications.

## Prerequisites

- Kubernetes cluster already running
- kubectl configured to access the cluster
- Helm v3+
- Discord webhook URL

## Files
```
ClusterRole-ClusterRoleBinding.yaml  
kubewatch-values.yaml  
kubewatch-nginx.yaml  
ClusterRole-ClusterRoleBinding.yaml
```
## Helm Repository

Add the Robusta Helm repository:
```
helm repo add robusta https://robusta-charts.storage.googleapis.com  
helm repo update  
```
## Install Kubewatch

Deploy Kubewatch using the provided values file:
```
helm upgrade --install kubewatch robusta/kubewatch \
  --namespace kubewatch \
  --create-namespace \
  -f kubewatch-values.yaml
```
This installation creates a new ServiceAccount named `kubewatch`, which is used by Kubewatch to access the Kubernetes API.

## RBAC Configuration

Apply the ClusterRole and ClusterRoleBinding to allow Kubewatch to watch Kubernetes resources:
```
kubectl apply -f ClusterRole-ClusterRoleBinding.yaml
```
The ClusterRole is bound to the `kubewatch` ServiceAccount and grants read access to the Kubernetes API for resource watch operations.

## Discord Webhook Secret

Kubewatch uses a Discord webhook URL stored in a Kubernetes Secret for sending notifications. Change the placeholder `<<webhook-url>>` then apply the manifest.

```
kubectl apply -f discord-webhook-secret.yaml
```

## NGINX Configuration for Discord

Apply the NGINX configuration used to reformat webhook requests before sending them to Discord:
```
kubectl apply -f kubewatch-nginx.yaml
```
This NGINX layer reformats Kubewatch webhook payloads to match Discord’s required request body format.

## Verify
```
kubectl get pods -n kubewatch  
kubectl get sa -n kubewatch kubewatch  
```
Events and notifications should now be sent to the configured Discord channel.
