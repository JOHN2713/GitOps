# 🚀 GitOps con ArgoCD y Kubernetes: Aplicación Hola Mundo

Este repositorio implementa un flujo **GitOps** utilizando **ArgoCD** y **Kubernetes**. La aplicación es una página simple **HTML** servida mediante **Nginx**.

## 📋 Descripción

El propósito de este proyecto es demostrar cómo usar **ArgoCD** para la automatización de despliegues con GitOps. Cada cambio en el repositorio se sincroniza automáticamente con un clúster de Kubernetes.

## 📂 Estructura del repositorio

```plaintext
GitOps/
│
├── my-app/                 # Carpeta principal de la aplicación
│   ├── html/               # Archivos HTML (Hola Mundo)
│   │   └── index.html      # Página principal servida por Nginx
│   ├── deployment.yaml     # Manifiesto de Deployment para Kubernetes
│   └── service.yaml        # Manifiesto de Service para exponer la app
│
├── argocd-install.yaml     # Instalación de ArgoCD en Kubernetes
├── kind-cluster.yaml       # Configuración de clúster Kind
├── metrics-server.yaml     # Configuración del Metrics Server
└── README.md               # Este archivo
```

## 🎯 Requisitos previos

Antes de comenzar, asegúrate de tener instaladas las siguientes herramientas:

- Docker
- Kubectl
- Kind
- ArgoCD CLI
- Git (para versionar y subir cambios)

## ⚙️ Configuración del entorno

### 1. Crear el clúster Kubernetes con Kind

Crea un clúster local usando el archivo `kind-cluster.yaml`:

```bash
kind create cluster --name my-gitops-cluster --config kind-cluster.yaml
```

### 2. Instalar ArgoCD

Instala ArgoCD en el clúster:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f argocd-install.yaml
```

Realiza un port-forward para acceder a ArgoCD localmente:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

- Accede a ArgoCD en: https://localhost:8080
- **Usuario:** `admin`
- **Contraseña:** Recupera la contraseña con:

```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 --decode
```

### 3. Construir y subir la imagen Docker

Construye la imagen Docker para servir el archivo **index.html** con Nginx:

```bash
docker build -t john2713/hello-world-app:latest .
docker push john2713/hello-world-app:latest
```

### 4. Configurar la aplicación en ArgoCD

1. Edita el archivo `application.yaml` para apuntar a tu repositorio Git.
2. Aplica la configuración:

```bash
kubectl apply -f application.yaml
```

3. ArgoCD sincronizará automáticamente la aplicación.

## 🌐 Acceder a la aplicación

### Opción 1: Port-forward

Usa port-forwarding para acceder localmente:

```bash
kubectl port-forward svc/hello-world-service -n default 8080:80
```

Abre tu navegador en: http://localhost:8080

### Opción 2: NodePort

Si configuraste el servicio como **NodePort**, accede usando:

```bash
kubectl get services -n default
```

Localiza el puerto y accede con `http://localhost:[PUERTO]`.

## 🧪 Prueba el flujo GitOps

1. Realiza un cambio en el archivo **index.html** (por ejemplo, actualiza el texto).
2. Sube los cambios al repositorio Git:

```bash
git add .
git commit -m "Actualizar página Hola Mundo"
git push
```

3. ArgoCD detectará el cambio y sincronizará el clúster automáticamente.
4. Verifica la actualización en **http://localhost:8080**

## 📊 Monitoreo

Para monitorear métricas de CPU y memoria en el clúster, instala el **Metrics Server**:

```bash
kubectl apply -f metrics-server.yaml
```

Verifica las métricas con:

```bash
kubectl top nodes
kubectl top pods -n default
```

## 📝 Licencia

[Especifica aquí la licencia de tu proyecto, por ejemplo: MIT License]

## 🤝 Contribuciones

Las contribuciones son bienvenidas. Por favor, lee las directrices de contribución antes de hacer un pull request.
