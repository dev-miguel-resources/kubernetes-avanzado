Laboratorio Práctico - Seguridad de Kubernetes

Objetivo principal:
Implementar y comprender los mecanismos de autenticación, autorización y endurecimiento de seguridad
dentro de Kubernetes, utilizando certificados x.509, Service Accounts, RBAC, cert-manager y Security Contexts.

Aprendizaje específico:
Entender la PKI interna del clúster.
Identificar el funcionamiento de certificados x.509.
Implementar certificados TLS utilizando cert-manager y OpenSSL.
Configurar RBAC utilizando Roles y ClusterRoles.
Trabajar con ServiceAccounts.
Validar restricciones de seguridad sobre contenedores.
Aplicar endurecimiento de seguridad mediante SecurityContexts.

Requisitos:
Docker Desktop.
Minikube con multinodo.
Kubectl.
Helm.
OpenSSL. (ya viene instalado en el cluster de minikube).

Paso 1: Crear un cluster con 3 nodos
minikube start --nodes 3
kubectl get nodes

Paso 2: Instalar Helm (Permite gestionar instalaciones automatizadas en kubernetes)
Documentación de instalación: https://helm.sh/docs/intro/install/
Windows: winget install Helm.Helm y luego ejecutar: helm version

Paso 3: Necesitar instalar Ingress para la gestión TLS
minikube addons enable ingress
kubectl get po -n ingress-nginx

Paso 4: Verificar acceso administrativo del cluster
kubectl auth can-i '*' '*' --all-namespaces (debe responder yes)

Paso 5: Verificar acceso al cluster
kubectl cluster-info
Resultado esperado:
Kubernetes control plane is running at https://127.0.0.1:58330
CoreDNS is running at https://127.0.0.1:58330/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

Paso 6. Ingresar al cluster como verificación rápida mediante terminal
minikube ssh
exit

Paso 7. Revisar certificados del cluster
minikube ssh
cd /var/lib/minikube/certs
ls

Paso 8. Revisar el kube-config
kubectl config view

Paso 9: Crear namespace
kubectl create ns security-lab
kubectl get ns