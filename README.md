# GoIT DevOps Helm Charts

This repository contains the **Helm charts used to deploy the Django application** in the Kubernetes cluster.

It is part of the CI/CD pipeline implemented with:

- Jenkins
- Terraform
- Amazon ECR
- Helm
- Argo CD

---

# Related repositories

Application repository  
https://github.com/PiotrJasinski1995/goit-devops-app

Infrastructure repository  
https://github.com/PiotrJasinski1995/goit-devops-infra

---

# Repository purpose

This repository stores the **Helm chart for the Django application**.

The chart is monitored by **Argo CD**, which automatically synchronizes the Kubernetes cluster when changes are detected in Git.

---

# Chart structure

```
charts/
└── django-app
    ├── Chart.yaml
    ├── values.yaml
    └── templates/
        ├── deployment.yaml
        ├── service.yaml
        ├── configmap.yaml
        └── hpa.yaml
```

---

# CI/CD workflow

The deployment pipeline works as follows:

1. Jenkins builds a Docker image from the application repository.
2. The image is pushed to **Amazon ECR**.
3. Jenkins updates the image tag in:

```
charts/django-app/values.yaml
```

4. Jenkins pushes the change to the Git repository.
5. **Argo CD detects the change and automatically synchronizes the application in the Kubernetes cluster.**

---

# Helm chart configuration

The most important configuration is located in:

```
charts/django-app/values.yaml
```

Example configuration:

```
replicaCount: 1

image:
  repository: <ECR repository>
  tag: latest
  pullPolicy: IfNotPresent

service:
  type: ClusterIP
  port: 8000
```

The `image.tag` value is automatically updated by the Jenkins pipeline.

---

# Argo CD deployment

Argo CD monitors this repository and deploys the application defined in the Helm chart.

When the `values.yaml` file is updated, Argo CD automatically updates the running application in the Kubernetes cluster.

---

# Autoscaling

The Helm chart includes a **Horizontal Pod Autoscaler (HPA)** configuration.

The autoscaler is defined in:

```
charts/django-app/templates/hpa.yaml
```

The HPA scales the Django application pods based on CPU utilization.

Example configuration:

- minimum replicas: 1
- maximum replicas: 3
- target CPU utilization: 70%

This ensures the application can automatically scale depending on workload.
