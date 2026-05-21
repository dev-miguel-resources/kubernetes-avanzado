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

kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.4/manifests/custom-resources.yaml

Verificar Pods de networking
kubectl get po -n kube-system