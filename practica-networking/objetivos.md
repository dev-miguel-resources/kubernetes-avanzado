Laboratorio - Networking Avanzado

# Objetivos Específicos:

Comprender la red plana de Kubernetes
Instalar una CNI real mediante Calico
Validar la comunicación multi-node
Comprender el flujo de red overlay networking
Implementar services y comprender el NAT
Aplicar Ingress Policies mediante deny mediante manifiestos
Aplicar Egress Policies mediante deny mediante manifiestos
Implementar Namespace isolation
Comprender el concepto de microsegmentación
Automatizaciones propias de un CNI avanzado

Parte 1 - Crear un cluster multi-node sin CNI default
para evitar conflictos de networking y utilizar únicamente Calico.

Crear clúster
minikube start --network-plugin=cni --cni=false --nodes 3

Verificar nodes
kubectl get nodes -o wide

Parte 2 - Instalar nuetro CNI - Calico
Con esto habilitamos el networking al cluster como si fuera un entorno real.

kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.4/manifests/calico.yaml

Verificar Pods de networking
kubectl get po -n kube-system

Parte 3 - Comprender y aplicar la segmentación automatizada de IP Kubernetes (Segmentación PODCIDRs)

kubectl get nodes -o jsonpath="{range .items[*]}{.metadata.name}{': '}{.spec.podCIDR}{'\n'}{end}"

Resultado:
minikube: 10.244.0.0/24
minikube-m02: 10.244.1.0/24
minikube-m03: 10.244.2.0/24

Parte 4 - Crear Namespaces
kubectl create namespace frontend
kubectl create namespace backend
kubectl get ns

Parte 5 - Crear Pods de networking
kubectl apply -f frontend.yaml
kubectl apply -f backend.yaml
kubectl get pods -A -o wide

Parte 6 - Validar red plana de Kubernetes

Comprobar la comunicación Pod a Pod

Entrar a un frontend Pod:
kubectl exec -it -n frontend frontend-app-78fb459d6-6dglt -- bash
kubectl get po -n backend -o wide (en otra terminal)
wget -qO- BACKEND_IP

Parte 7 - Ver interfaces de redes Linux
minikube ssh
Revisar sus interfaces
 -> cni0, vxlan.calico, vethXXXX, tunl0

Parte 8 - Crear Service ClusterIP
kubectl apply -f frontend-service.yaml
kubectl get svc -A

Parte 9 - Visualizar el kube-proxy e iptables

minikube ssh
sudo iptables -t nat -L

Parte 10 - Namespace Isolation
Bloquear tráfico entre namespaces.

kubectl apply -f deny-all.yaml
kubectl get po -n frontend -o wide
kubectl exec -it -n frontend POD_NAME -- bash
kubectl get po -n backend -o wide (en otra terminal)
wget -qO- BACKEND_IP