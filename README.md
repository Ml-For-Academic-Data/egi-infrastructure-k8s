# Infrastructura K8s

> Infraestructura como código para la plataforma EGI utilizando Kubernetes, Helm y ArgoCD con soporte para desarrollo local

[![Kubernetes](https://img.shields.io/badge/Kubernetes-v1.20+-326CE5?style=flat&logo=kubernetes)](https://kubernetes.io/)
[![Helm](https://img.shields.io/badge/Helm-v3.0+-0F1689?style=flat&logo=helm)](https://helm.sh/)
[![ArgoCD](https://img.shields.io/badge/ArgoCD-GitOps-00D4FF?style=flat&logo=argo)](https://argoproj.github.io/cd/)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=flat&logo=docker)](https://docs.docker.com/compose/)

## 🎯 ¿Qué hace el proyecto?

Este repositorio contiene la **infraestructura como código** para la plataforma de un sistema completo de análisis de datos académicos que proporciona:

### Funcionalidades Principales

- **📊 Pipeline de Datos**: Orquestación automatizada de ETL con Apache Airflow
- **📈 Visualización Interactiva**: Dashboard con análisis estadísticos usando Panel (Python)
- **🌐 Portal Web**: Interfaz de usuario con Flask para acceso a las aplicaciones
- **🔄 GitOps**: Despliegue automatizado con ArgoCD
- **☁️ Cloud Native**: Infraestructura escalable en Kubernetes
- **🛠️ Desarrollo Local**: Entorno completo con Docker Compose

### Casos de Uso

- Análisis de rendimiento académico de estudiantes
- Visualización de métricas educativas en tiempo real
- Automatización de procesos de datos universitarios
- Dashboard ejecutivo para toma de decisiones

## 🏗️ Arquitectura

### Arquitectura de Producción (Kubernetes)
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Static Web    │    │   Dashboard     │    │    Airflow      │
│   (Flask)       │    │   (Panel)       │    │   (Backend)     │
│     :3000       │    │     :5000       │    │     :8080       │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
                    ┌─────────────────┐
                    │  NGINX Ingress  │
                    │   Controller    │
                    └─────────────────┘
                                 │
                    ┌─────────────────┐
                    │   AWS EFS       │
                    │ (Shared Storage)│
                    └─────────────────┘
```

### Arquitectura de Desarrollo (Docker Compose)
```
┌─────────────────┐    ┌─────────────────┐
│  Panel Dashboard│    │   Flask Portal  │
│     :5000       │    │     :3000       │
│   (Container)   │    │   (Container)   │
└─────────────────┘    └─────────────────┘
         │                       │
         └───────────────────────┘
                     │
        ┌─────────────────────────┐
        │    Shared Volume        │
        │   (Local Directory)     │
        └─────────────────────────┘
```

### Flujo de GitOps
```
GitHub Repository → ArgoCD → Kubernetes Cluster
       │              │            │
   [Git Push]     [Sync Apps]  [Deploy Pods]
       │              │            │
   [CI/CD Tags]   [Auto Heal]  [Health Check]
```

## 📁 Estructura del Proyecto

```
egi-infrastructure-k8s/
├── README.md                           # Esta documentación
├── .gitignore                          # Archivos ignorados por Git
├── requirements.txt                    # Dependencias Python
├── docker-compose.yml                 # Desarrollo local
│
├── applications/                       # 📦 Definiciones ArgoCD
│   ├── app-of-apps.yaml              # Aplicación raíz
│   ├── backend-app-desarrollo.yaml    # Backend para desarrollo
│   ├── frontend-app-desarrollo.yaml   # Frontend para desarrollo
│   ├── static-web-app-desarrollo.yaml # Portal web para desarrollo
│   ├── ingress-app-desarrollo.yaml    # Ingress para desarrollo
│   ├── main-ingress-app.yaml         # Ingress principal
│   ├── nginx-ingress-app.yaml        # NGINX Controller
│   └── storage-application.yaml       # Configuración de almacenamiento
│
├── charts/                            # 📊 Helm Charts
│   ├── backend/                       # Chart para Airflow
│   │   ├── Chart.yaml                # Metadatos del chart
│   │   ├── values.yaml               # Valores base
│   │   ├── values-desarrollo.yaml    # Valores para desarrollo
│   │   ├── values-pruebas.yaml       # Valores para testing
│   │   ├── values-despliegue.yaml    # Valores para producción
│   │   └── templates/                # Plantillas Kubernetes
│   │       ├── deployment.yaml       # Deployment Airflow Webserver
│   │       ├── scheduler-deployment.yaml # Deployment Airflow Scheduler
│   │       ├── service.yaml          # Service para Airflow
│   │       ├── mysql-deployment.yaml # Deployment MySQL
│   │       ├── mysql-service.yaml    # Service MySQL
│   │       ├── mysql-pvc.yaml        # PVC para MySQL
│   │       ├── mysql-secret.yaml     # Credenciales MySQL
│   │       └── airflow-admin-secret.yaml # Credenciales Admin
│   │
│   ├── frontend/                      # Chart para Dashboard Panel
│   │   ├── Chart.yaml
│   │   ├── values.yaml
│   │   ├── values-desarrollo.yaml
│   │   ├── values-pruebas.yaml
│   │   ├── values-despliegue.yaml
│   │   └── templates/
│   │       ├── deployment.yaml       # Deployment genérico
│   │       └── service.yaml          # Service genérico
│   │
│   ├── frontend-static/               # Chart para Portal Flask
│   │   ├── Chart.yaml
│   │   ├── values.yaml
│   │   ├── values-desarrollo.yaml
│   │   └── templates/
│   │       ├── deployment.yaml       # Deployment con variables ENV
│   │       └── service.yaml          # Service genérico
│   │
│   └── main-ingress/                  # Chart para Ingress
│       ├── Chart.yaml
│       ├── values.yaml
│       └── templates/
│           └── ingress.yaml           # Reglas de enrutamiento
│
├── rbac/                              # 🔐 Configuraciones RBAC
│   └── desarrollo-permissions.yaml    # Permisos para ArgoCD
│
├── storage/                           # 💾 Configuración de almacenamiento
│   ├── storageclass.yaml             # StorageClass para EFS
│   ├── persistentvolume.yaml         # PV para AWS EFS
│   └── persistentvolumeclaim.yaml    # PVC compartido
│
├── templates/                         # 🎨 Templates Flask (desarrollo local)
│   ├── index.html                    # Portal principal
│   └── access_denied.html            # Página de error
│
└── visualization/                     # 📊 Aplicación Panel (desarrollo local)
    └── app.py                        # Dashboard interactivo
```

## 🔧 Dependencias

### Infraestructura Base
- **Kubernetes Cluster** (v1.20+)
- **Helm** (v3.0+) 
- **kubectl** configurado con acceso al cluster

### Componentes de Infraestructura
- **ArgoCD** (para GitOps)
- **NGINX Ingress Controller** (para enrutamiento)
- **AWS EFS CSI Driver** (para almacenamiento compartido)

### Dependencias de Desarrollo Local
- **Docker** (v20.10+)
- **Docker Compose** (v2.0+)
- **Python** (v3.8+) con Flask

### Registros de Contenedores
- **GitHub Container Registry** (ghcr.io)
  - `ghcr.io/ml-for-academic-data/egi-backend` (Airflow)
  - `ghcr.io/ml-for-academic-data/egi-dashboard` (Panel)
  - `ghcr.io/ml-for-academic-data/egi-static-web` (Flask)

### Repositorios Externos
- **egi-control**: Repositorio con datos y DAGs de Airflow
- **ingress-nginx**: Chart oficial de NGINX

## 🚀 Cómo se usa

### 1. Uso en Desarrollo Local

```bash
# Clonar el repositorio
git clone https://github.com/Ml-For-Academic-Data/egi-infrastructure-k8s.git
cd egi-infrastructure-k8s

# Levantar servicios locales
docker-compose up -d

# Acceder a las aplicaciones
open http://localhost:3000  # Portal Flask
open http://localhost:5000  # Dashboard Panel
```

### 2. Uso en Kubernetes

```bash
# Aplicar la aplicación raíz
kubectl apply -f applications/app-of-apps.yaml

# Verificar aplicaciones en ArgoCD
kubectl get applications -n argocd

# Acceder via Ingress
curl http://your-cluster-ip/          # Portal
curl http://your-cluster-ip/panel     # Dashboard  
curl http://your-cluster-ip/airflow   # Airflow
```

### 3. Comandos Útiles

```bash
# Ver estado de pods
kubectl get pods -n desarrollo

# Ver logs de Airflow
kubectl logs -f deployment/backend-airflow-webserver -n desarrollo

# Ver configuración de Ingress
kubectl describe ingress main-ingress -n desarrollo

# Sincronizar aplicación específica
argocd app sync egi-backend-desarrollo
```

## ⚙️ Cómo se configura

### 1. Configuración de Ambientes

Cada componente tiene archivos de configuración por ambiente:

```yaml
# values.yaml - Configuración base
# values-desarrollo.yaml - Auto-actualizado por CI/CD
# values-pruebas.yaml - Actualización manual
# values-despliegue.yaml - Actualización manual para producción
```

### 2. Variables de Entorno

**Frontend Static (Portal Flask):**
```yaml
env:
  AIRFLOW_URL: "http://54.210.81.250:32693"
  PANEL_URL: "http://54.210.81.250:30726"
```

**Airflow Backend:**
```yaml
airflow:
  fernetKey: "D_b7-k2tJcEmrfAPASL8V_cY03LJd-uWWtJ-8HN1Esk="
  dbConnection: "mysql+pymysql://user:1234@mysql-server.desarrollo.svc.cluster.local/students"
```

### 3. Configuración de Almacenamiento

**AWS EFS Configuration:**
```yaml
# storage/persistentvolume.yaml
csi:
  driver: efs.csi.aws.com
  volumeHandle: fs-0ab9b178d52782307.efs.us-east-1.amazonaws.com
```

### 4. Secretos Requeridos

```bash
# Secreto para GitHub Container Registry
kubectl create secret docker-registry ghcr-credentials \
  --docker-server=ghcr.io \
  --docker-username=<username> \
  --docker-password=<token> \
  --namespace=desarrollo

# Secretos creados automáticamente por Helm:
# - mysql-credentials (MySQL passwords)
# - airflow-admin-credentials (Airflow admin password)
```

### 5. Configuración de Ingress

```yaml
# charts/main-ingress/values.yaml
paths:
  static_web:
    serviceName: egi-static-web-desarrollo-static-web-service
    servicePort: 3000
  panel:
    serviceName: egi-frontend-desarrollo-visualization-service  
    servicePort: 5000
  airflow:
    serviceName: backend-airflow-webserver
    servicePort: 8080
```

## 🎯 Cómo se despliega

### 1. Despliegue Inicial

```bash
# 1. Instalar prerequisitos en el cluster
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# 2. Instalar NGINX Ingress Controller  
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install nginx-ingress ingress-nginx/ingress-nginx \
  --namespace ingress-nginx --create-namespace

# 3. Configurar almacenamiento EFS
kubectl apply -f storage/

# 4. Aplicar permisos RBAC
kubectl apply -f rbac/desarrollo-permissions.yaml

# 5. Crear secretos necesarios
kubectl create secret docker-registry ghcr-credentials \
  --docker-server=ghcr.io \
  --docker-username=<username> \
  --docker-password=<token> \
  --namespace=desarrollo

# 6. Desplegar aplicación raíz
kubectl apply -f applications/app-of-apps.yaml
```

### 2. Despliegue Automático (GitOps)

El despliegue se realiza automáticamente cuando:

```
1. CI/CD Pipeline actualiza tags en values-desarrollo.yaml
2. ArgoCD detecta cambios en el repositorio
3. ArgoCD sincroniza automáticamente las aplicaciones
4. Kubernetes despliega los nuevos pods
```

### 3. Despliegue Manual por Ambientes

**Desarrollo (Automático):**
```bash
# Los tags se actualizan automáticamente vía CI/CD
# ArgoCD sincroniza cada 3 minutos
```

**Pruebas (Manual):**
```bash
# 1. Actualizar tag en values-pruebas.yaml
# 2. Hacer commit y push
# 3. ArgoCD sincronizará automáticamente
```

**Producción (Manual):**
```bash
# 1. Actualizar tag en values-despliegue.yaml  
# 2. Aumentar réplicas si es necesario
# 3. Hacer commit y push
# 4. Verificar sincronización en ArgoCD
```

### 4. Verificación del Despliegue

```bash
# Verificar aplicaciones ArgoCD
kubectl get applications -n argocd

# Verificar pods
kubectl get pods -n desarrollo

# Verificar servicios
kubectl get svc -n desarrollo

# Verificar ingress
kubectl get ingress -n desarrollo

# Ver logs
kubectl logs -f deployment/backend-airflow-webserver -n desarrollo
```

## 💻 Desarrollo Local

### 1. Setup Inicial

```bash
# Clonar repositorio
git clone https://github.com/Ml-For-Academic-Data/egi-infrastructure-k8s.git
cd egi-infrastructure-k8s

# Instalar dependencias Python
pip install -r requirements.txt

# Verificar Docker
docker --version
docker-compose --version
```

### 2. Levantar Entorno Local

```bash
# Levantar todos los servicios
docker-compose up -d

# Ver logs
docker-compose logs -f

# Verificar servicios
docker-compose ps
```

### 3. Servicios Disponibles

| Servicio | URL Local | Descripción |
|----------|-----------|-------------|
| **Portal Flask** | http://localhost:3000 | Página principal con enlaces a las apps |
| **Dashboard Panel** | http://localhost:5000 | Visualizaciones interactivas de datos |

### 4. Desarrollo y Hot Reload

```bash
# El volumen está configurado para hot reload
# Los cambios en ./visualization se reflejan automáticamente

# Para desarrollo del Portal Flask
export FLASK_ENV=development
python app.py  # Si existe un app.py local
```

### 5. Debugging Local

```bash
# Ver logs de un servicio específico
docker-compose logs panel-app

# Entrar a un contenedor
docker-compose exec panel-app /bin/bash

# Reiniciar un servicio
docker-compose restart panel-app

# Parar todos los servicios
docker-compose down
```

## 🧩 Componentes

### Backend (Airflow)
- **Imagen**: `ghcr.io/ml-for-academic-data/egi-backend`
- **Puerto**: 8080
- **Función**: Orquestación de workflows de datos
- **Base de datos**: MySQL 8.0
- **Almacenamiento**: AWS EFS compartido
- **Componentes**:
  - Webserver (UI de Airflow)
  - Scheduler (Ejecutor de tareas)
  - MySQL (Base de datos)

### Frontend Dashboard (Panel)
- **Imagen**: `ghcr.io/ml-for-academic-data/egi-dashboard`
- **Puerto**: 5000
- **Función**: Visualizaciones interactivas con Python Panel
- **Tecnologías**: Pandas, Plotly, Matplotlib, Seaborn
- **Datos**: Lee desde `/opt/airflow/data/processed/`

### Static Web (Flask Portal)
- **Imagen**: `ghcr.io/ml-for-academic-data/egi-static-web`
- **Puerto**: 3000
- **Función**: Portal de entrada con enlaces a aplicaciones
- **Tecnología**: Flask (Python)
- **Templates**: HTML con estilos modernos

### Ingress (NGINX)
- **Controlador**: NGINX Ingress Controller
- **Función**: Enrutamiento de tráfico
- **Rutas**:
  - `/` → Static Web
  - `/panel` → Dashboard
  - `/airflow` → Airflow WebUI

### Storage (AWS EFS)
- **Tipo**: Amazon Elastic File System
- **Modo**: ReadWriteMany (compartido)
- **Función**: Almacenamiento persistente para datos
- **Montaje**: `/opt/airflow/data` en todos los pods

## 🌍 Ambientes

### Desarrollo (desarrollo)
- **Namespace**: `desarrollo`
- **Actualización**: Automática vía CI/CD
- **Réplicas**: 1 por servicio
- **Propósito**: Testing continuo y desarrollo

### Pruebas (pruebas)  
- **Namespace**: `pruebas`
- **Actualización**: Manual
- **Réplicas**: 1 por servicio
- **Propósito**: Testing de calidad antes de producción

### Producción (despliegue)
- **Namespace**: `produccion`
- **Actualización**: Manual
- **Réplicas**: 2+ por servicio
- **Propósito**: Ambiente de producción

### Configuración por Ambiente

```yaml
# Ejemplo: Backend values por ambiente
desarrollo:
  replicas: 1
  image:
    tag: "a9f407f"  # Auto-actualizado

pruebas:
  replicas: 1  
  image:
    tag: "not-deployed-yet"  # Manual

produccion:
  replicas: 2
  image:
    tag: "not-deployed-yet"  # Manual
```

## 📊 Monitoreo

### 1. Verificación de Salud

```bash
# Estado de aplicaciones ArgoCD
kubectl get applications -n argocd

# Estado de pods
kubectl get pods -n desarrollo

# Estado de servicios
kubectl get svc -n desarrollo

# Estado de ingress
kubectl describe ingress main-ingress -n desarrollo
```

### 2. Logs de Aplicaciones

```bash
# Logs de Airflow Webserver
kubectl logs -f deployment/backend-airflow-webserver -n desarrollo

# Logs de Airflow Scheduler  
kubectl logs -f deployment/backend-airflow-scheduler -n desarrollo

# Logs del Dashboard
kubectl logs -f deployment/egi-frontend-desarrollo-visualization -n desarrollo

# Logs del Portal
kubectl logs -f deployment/egi-static-web-desarrollo-static-web -n desarrollo
```

### 3. Métricas de Recursos

```bash
# Uso de recursos por pods
kubectl top pods -n desarrollo

# Uso de recursos por nodos
kubectl top nodes

# Descripción detallada de un pod
kubectl describe pod <pod-name> -n desarrollo
```

### 4. Verificación de Conectividad

```bash
# Test de conectividad entre servicios
kubectl exec -it <pod-name> -n desarrollo -- curl http://mysql-server:3306

# Verificar DNS interno
kubectl exec -it <pod-name> -n desarrollo -- nslookup mysql-server
```

## 🔧 Troubleshooting

### Problemas Comunes

#### 1. Pods en estado Pending
```bash
# Verificar recursos disponibles
kubectl describe nodes

# Verificar eventos del pod
kubectl describe pod <pod-name> -n desarrollo

# Posibles causas:
# - Recursos insuficientes (CPU/Memory)
# - PVC no puede ser montado
# - NodeSelector no coincide
```

#### 2. Problemas de Almacenamiento EFS
```bash
# Verificar PV y PVC
kubectl get pv,pvc -n desarrollo

# Verificar StorageClass
kubectl get storageclass

# Verificar que el EFS ID sea correcto
kubectl describe pv efs-shared-pv

# Solución común:
# 1. Verificar que el EFS CSI Driver esté instalado
# 2. Verificar que el volumeHandle sea correcto
# 3. Verificar permisos de seguridad del EFS
```

#### 3. Aplicaciones ArgoCD no sincronizadas
```bash
# Ver estado de aplicación
argocd app get <app-name>

# Forzar sincronización
argocd app sync <app-name>

# Ver logs de ArgoCD
kubectl logs -f deployment/argocd-application-controller -n argocd

# Causas comunes:
# - Error en los manifiestos YAML
# - Permisos RBAC insuficientes
# - Repositorio no accesible
```

#### 4. Problemas de Ingress/Conectividad
```bash
# Verificar NGINX Ingress Controller
kubectl get pods -n ingress-nginx

# Verificar configuración de ingress
kubectl describe ingress main-ingress -n desarrollo

# Test de conectividad
curl -v http://<cluster-ip>/
curl -v http://<cluster-ip>/panel
curl -v http://<cluster-ip>/airflow

# Soluciones:
# 1. Verificar que los nombres de servicios coincidan
# 2. Verificar que los puertos sean correctos
# 3. Revisar anotaciones del ingress
```

#### 5. Problemas de Imágenes
```bash
# Verificar pull de imágenes
kubectl describe pod <pod-name> -n desarrollo

# Error común: ImagePullBackOff
# Solución:
# 1. Verificar que el secreto ghcr-credentials exista
# 2. Verificar que el tag de imagen sea correcto
# 3. Verificar permisos en GitHub Container Registry

kubectl get secrets -n desarrollo
kubectl describe secret ghcr-credentials -n desarrollo
```

### Comandos de Diagnóstico

```bash
# Salud general del cluster
kubectl cluster-info

# Eventos recientes
kubectl get events --sort-by=.metadata.creationTimestamp -n desarrollo

# Recursos del cluster
kubectl get all -n desarrollo

# Verificar configuración de contexto
kubectl config current-context
kubectl config get-contexts
```

### CI/CD Pipeline

```yaml
# El pipeline actualiza automáticamente:
# - values-desarrollo.yaml con nuevos tags
# - ArgoCD sincroniza automáticamente
# - Para otros ambientes, actualización manual
```

### Estructura de Commits

```bash
# Usar prefijos descriptivos
feat: add new dashboard component
fix: resolve ingress routing issue  
docs: update README with new setup steps
chore: update dependencies
refactor: reorganize helm chart structure
```

---

**Equipo Byte Builders** - [ML For Academic Data](https://github.com/Ml-For-Academic-Data)
