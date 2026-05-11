# -----------------------------
# 1. Ver nodos del cluster
# -----------------------------
kubectl get nodes

NAME           STATUS   ROLES           AGE     VERSION
minikube       Ready    control-plane   2m13s   v1.32.0
minikube-m02   Ready    <none>          94s     v1.32.0
minikube-m03   Ready    <none>          68s     v1.32.0

# -----------------------------
# 2. Ver labels de los nodos
# -----------------------------
kubectl get nodes --show-labels

# -----------------------------
# 3. Agregar labels de prueba
# -----------------------------
# Nodo con GPU
kubectl label node minikube-m02 gpu=true (solo nodo 2)

Resultado esperado:
node/minikube-m02 labeled

# Nodo en otra zona
kubectl label node minikube-m02 zone=us-east-1a (no es considerada)

Resultado esperado:
node/minikube-m02 labeled

# Nodo en otra zona
kubectl label node minikube-m03 zone=us-east-1b (nodo 3)

Resultado esperado:
node/minikube-m03 labeled

# Nodo con SSD rápido
kubectl label node minikube-m02 fast-ssd (nodo 2)
kubectl label node minikube-m02 fast-ssd=true

Resultado esperado:
node/minikube-m02 labeled

# Nodo en mantenimiento
kubectl label node minikube-m03 maintenance=true (nodo 3)

Resultado esperado:
node/minikube-m03 labeled

# -----------------------------
# 4. Crear taint en nodo GPU
# -----------------------------
kubectl taint node minikube-m02 gpu=ml:NoSchedule

Resultado esperado:
node/minikube-m02 tainted

# -----------------------------
# 5. Aplicar Deployment
# -----------------------------
kubectl apply -f deployment-affinity.yml

Resultado:
deployment.apps/deploy-affinity created

# -----------------------------
# 6. Ver pods
# -----------------------------
kubectl get po

# -----------------------------
# 7. Ver en qué nodo quedaron
# -----------------------------
kubectl get po -o wide
