Parte 1 - Static Pods y Mirror Pods

Comprender:
Que son los Static Pods
Como los administra kubelet
Que son los Mirror Pods
Diferencias con Pods normales administrados por el API Server

Paso 1 - Crear directorio de manifests estáticos

En Minikube: minikube ssh

Verificamos la ruta de manifests:

ls /etc/kubernetes/manifests

Crear archivo:

sudo vi /etc/kubernetes/manifests/static-nginx.yaml
sudo rm /etc/kubernetes/manifests/.static-nginx.yaml.swp (eliminar la referencia del manifiesto)

Parte 2 - Init Containers
Objetivos:

Aprender inicialización previa
Dependencias
Esperas antes del arranque principal
Deployment con Init Container

kubectl apply -f init-container.yml
kubectl get po
kubectl get deploy
kubectl logs app-init-554b99b7ff-sczsj -c init-check (log de init containers)
kubectl logs app-init-554b99b7ff-sczsj (log app principal)

Parte 3 - Patrones de diseño: Sidecar

Objetivos:

Contenedores Auxiliares

kubectl logs sidecar-demo -c sidecar
kubectl logs -f sidecar-demo -c sidecar

Parte 4 - Patrones de diseño: Ambassador

La idea es que la app principal no se conecte directamente al recurso final, sino que hable con el ambassador, y este se encargue de la comunicación real.

Sin Ambassador
Código Java: requests.get("http://app-service:8080")

Entonces mi app1 está acoplada:
hostname, puerto, red, tls, autenticación, etc...

Con ambassador
Código Java: requests.get("http://localhost:9000")
y el ambassador se encarga de:
localhost:9000 --> app-service:8080

App1 --> Ambassador -> App2

Parte 5 - Adapter Pattern

Objetivo:

Transformar formatos de salida.

"La aplicación produce datos, el adapter los convierte
en un formato entendible para otros sistemas".