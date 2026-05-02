# Tarea 08 - Kubernetes con Minikube

## Descripción

En esta actividad se creó un clúster local de Kubernetes utilizando **Minikube**.  
Dentro del clúster se instalaron **Traefik** y **ArgoCD** mediante archivos YAML.

Traefik se utilizó para manejar las rutas del clúster mediante `IngressRoute`, mientras que ArgoCD se instaló para administrar aplicaciones dentro de Kubernetes. También se desplegó una aplicación web dockerizada usando Nginx.

## Tecnologías utilizadas

- Kubernetes
- Minikube
- Docker
- Traefik
- ArgoCD
- YAML
- GitHub

## Rama utilizada

```text
assignment-08
```

La rama fue creada a partir de `main`.

## Dominios locales configurados

```text
app.alex-dlg.com
argo.alex-dlg.com
```

## Configuración DNS local

Se editó el archivo `hosts` de Windows:

```text
C:\Windows\System32\drivers\etc\hosts
```

Y se agregaron las siguientes líneas:

```text
127.0.0.1 app.alex-dlg.com
127.0.0.1 argo.alex-dlg.com
```

Con esta configuración, los dominios locales apuntan hacia `127.0.0.1`, permitiendo acceder a las aplicaciones desplegadas en el clúster.

## Estructura del proyecto

```text
Tarea-08---Kubernetes/
│
├── app-src/
│   └── index.html
│
├── k8s/
│   ├── namespaces.yaml
│   ├── traefik/
│   │   ├── crds.yaml
│   │   ├── rbac.yaml
│   │   └── traefik.yaml
│   ├── argocd/
│   │   ├── install.yaml
│   │   ├── argocd-cmd-params.yaml
│   │   └── argocd-ingressroute.yaml
│   └── app/
│       ├── deployment.yaml
│       ├── service.yaml
│       └── ingressroute.yaml
│
├── scripts/
│   └── deploy.ps1
│
├── Dockerfile
└── README.md
```

## Dockerización de la aplicación

La aplicación fue dockerizada utilizando Nginx como servidor web.

```dockerfile
FROM nginx:stable-alpine

COPY app-src/index.html /usr/share/nginx/html/index.html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

La imagen utilizada para el despliegue fue:

```text
assignment-08-app:local
```

## Comandos ejecutados

### Crear e iniciar el clúster

```bash
C:\minikube\minikube.exe start --driver=docker --cpus=4 --memory=4096
```

### Verificar el estado del clúster

```bash
C:\minikube\minikube.exe status
C:\minikube\minikube.exe kubectl -- get nodes
```

### Construir la imagen Docker

```bash
C:\minikube\minikube.exe image build -t assignment-08-app:local .
```

### Aplicar namespaces

```bash
C:\minikube\minikube.exe kubectl -- apply -f k8s\namespaces.yaml
```

### Instalar Traefik

```bash
C:\minikube\minikube.exe kubectl -- apply -f k8s\traefik\crds.yaml
C:\minikube\minikube.exe kubectl -- apply -f k8s\traefik\rbac.yaml
C:\minikube\minikube.exe kubectl -- apply -f k8s\traefik\traefik.yaml
```

### Instalar ArgoCD

```bash
C:\minikube\minikube.exe kubectl -- apply -n argocd --server-side --force-conflicts -f k8s\argocd\install.yaml
C:\minikube\minikube.exe kubectl -- apply -f k8s\argocd\argocd-cmd-params.yaml
C:\minikube\minikube.exe kubectl -- rollout restart deployment argocd-server -n argocd
C:\minikube\minikube.exe kubectl -- apply -f k8s\argocd\argocd-ingressroute.yaml
```

### Desplegar la aplicación

```bash
C:\minikube\minikube.exe kubectl -- apply -f k8s\app\deployment.yaml
C:\minikube\minikube.exe kubectl -- apply -f k8s\app\service.yaml
C:\minikube\minikube.exe kubectl -- apply -f k8s\app\ingressroute.yaml
```

### Exponer Traefik localmente

```bash
C:\minikube\minikube.exe kubectl -- -n traefik port-forward --address 127.0.0.1 svc/traefik 80:80
```

Este comando debe mantenerse activo mientras se accede a los dominios locales desde el navegador.

## Manifiestos utilizados

### Traefik

```text
k8s/traefik/crds.yaml
k8s/traefik/rbac.yaml
k8s/traefik/traefik.yaml
```

### ArgoCD

```text
k8s/argocd/install.yaml
k8s/argocd/argocd-cmd-params.yaml
k8s/argocd/argocd-ingressroute.yaml
```

Dominio configurado para ArgoCD:

```yaml
match: Host(`argo.alex-dlg.com`)
```

### Aplicación web

```text
k8s/app/deployment.yaml
k8s/app/service.yaml
k8s/app/ingressroute.yaml
```

Dominio configurado para la aplicación:

```yaml
match: Host(`app.alex-dlg.com`)
```

## Verificación del clúster

```bash
C:\minikube\minikube.exe kubectl -- get pods -A
C:\minikube\minikube.exe kubectl -- get svc -A
C:\minikube\minikube.exe kubectl -- get ingressroute -A
```

## Repositorio

```text
https://github.com/AlexDLG1/Tarea-08---Kubernetes
```

Rama de entrega:

```text
assignment-08
```

---

# Evidencias

![Captura 0](evidencias/cap0.png)

![Captura 1](evidencias/cap1.png)

![Captura 2](evidencias/cap2.png)

![Captura 3](evidencias/cap3.png)

![Captura 4](evidencias/cap4.png)

![Captura 5](evidencias/cap5.png)
