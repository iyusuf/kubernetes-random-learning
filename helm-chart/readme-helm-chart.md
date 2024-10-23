# Helm, Getting Started

## Environment Prep

To get started with Helm and package your existing 3-tier application into Helm charts, follow these steps:

### 1. **Install Helm**
If Helm is not installed yet, you can install it by running:
```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```
Verify the installation:
```bash
helm version
```

### 2. **Create a Helm Chart**
Create a Helm chart for your application:
```bash
helm create myapp
```
This will create a basic directory structure for your Helm chart:
```
myapp/
  ├── Chart.yaml       # Metadata about the chart
  ├── values.yaml      # Default values for templates
  └── templates/       # Kubernetes manifests (deployments, services, etc.)
```

### 3. **Customize the Templates**

[[todo]]


## Migrate Existing 3 Tier Web App to Helm

- Migrate Postgres Service
    -  Migrate all required Kubernetes manifests file under dbserver folder.
- Migrate FastAPI Service
    - Migrate Kubernetes manifests file under fastapi-backend folder.
- Migrate Frontend 
    - Migrate js-frontend simple web page made with Javascript and HTML.
    - Folder location <<project root folder>>/samehost-3tier/js-frontend