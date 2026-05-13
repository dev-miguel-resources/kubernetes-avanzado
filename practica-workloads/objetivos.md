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