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

Paso 4: Ejercicios con Cluster Autoscaler + HPA
kubectl apply -f autoscale-demo1.yaml
kubectl get po
kubectl autoscale deployment autoscale-demo --cpu-percent=50 --min=1 --max=20
kubectl get hpa
kubectl top po (ver el consumo real de todas las aplicaciones)
kubectl get hpa -w (observando consumo actual de cpu%)
kubectl get po
kubectl get po -w (observar la cantidad de aplicaciones actuales)
kubectl get deploy autoscale-demo -w (observar cantidad de apps desde el deploy)

Escalado manual sin HPA
kubectl apply -f autoscale-demo2.yaml
kubectl scale deploy autoscale-demo --replicas=20
kubectl get po

Paso 5: Manejo de estados/pruebas de salud (Probes) y (Handlers)
kubectl apply -f probes-demo.yaml
kubectl get po -w

Paso 6. Aplicar políticas de disrupción (PDB) para asegurar disponibilidad de recursos
antes manipulaciones voluntarias
kubectl apply -f deployment.yaml
kubectl get po -o wide
kubectl apply -f pdb.yaml
kubectl get pdb
kubectl get nodes
kubectl drain minikube --ignore-daemonsets --delete-emptydir-data --force
kubectl get po -o wide -w

Paso 7: Troubleshotting
CrashLoopBackOff
kubectl apply -f crash-app.yaml
kubectl get po
kubectl get po -w
kubectl describe po crash-app
kubectl get nodes
kubectl uncordon minikube

OomKilled
kubectl apply -f oom-app.yaml
kubectl get po
kubectl get po -w
kubectl describe po oom-test
kubectl logs oom-test

ErrorImagePullbackOff
kubectl apply -f image-bad.yaml
kubectl get po
kubectl delete po bad-image
kubectl apply -f image-bad.yaml
kubectl get po

Bloqueos de Nodo (Cordon)
kubectl cordon minikube
kubectl uncordon minikube
kubectl get nodes

