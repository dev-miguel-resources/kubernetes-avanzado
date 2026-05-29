Paso 1: Metrics Server + Monitoreo con top

Instalar y validar Metrics Server
Usar top nodes y top pods
Generar cargas para observar consumo real de CPU/memoria
Entender como kubernetes obtiene métricas en tiempo real

Instalación de metrics server
minikube addons list
minikube addons enable metrics-server
kubectl get po -n kube-system

Observabilidad de consumo de los nodos
kubectl top nodes
kubectl describe nodes minikube

Si se quisiera generar pruebas de carga de un determinado nodo:
kubectl label node minikube-02 stress=true
kubectl get nodes --show-labels
agregas la referencia del nodo en el pod de carga
kubectl label node minikube-m02 stress=true

Ver consumo de pods
kubectl create deployment nginx --image=nginx
kubectl scale deployment nginx --replicas=3
kubectl top pods
kubectl apply -f cpu-burn.yaml
kubectl top pod cpu-burn
kubectl top pods

Paso 2: Ejercicio con HPA (Horizontal Pod Autoscaler)
kubectl autoscale deploy nginx --cpu-percent=50 --min=2 --max=6
kubectl get hpa

Cálculo: (Metrics Server)
2 pods actuales
Objetivo = 50%
CPU promedio = 60%
2 X 60/50 = 3 -> tu ya tienes 2 agrega 1 más

2 pods actuales
Objetivo = 50%
CPU promedio = 180%

2 x 180/50 = 8 -> acá ocuparía el máximo que serían 6

Paso 3: Ejercicio con VPA (Vertical Pod Autoscaler)
Instalar VPA:
helm install vpa cowboysysop/vertical-pod-autoscaler
helm repo add cowboysysop https://cowboysysop.github.io/charts/
helm repo update
helm install vpa cowboysysop/vertical-pod-autoscaler
helm status vpa --namespace default
kubectl apply -f vpa.yaml
kubectl get vpa
kubectl describe vpa nginx-vpa