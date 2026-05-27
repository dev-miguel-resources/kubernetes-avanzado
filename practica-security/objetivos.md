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

Paso 10: Verificar el proveedor interno de certificados
minikube ssh
openssl version

Paso 11: Crear una cuenta de servicio (ServiceAccount)
kubectl create sa app-sa -n security-lab
kubectl get sa -n security-lab
kubectl describe sa app-sa -n security-lab

Paso 12: Ver como se crea un token de autenticación para la sa
kubectl create token app-sa -n security-lab

Paso 13: Instalar cert-manager (gestor de certificaciones que trabaja con OpenSSL)
Agregar el repositorio Helm: (Charts)
helm repo add jetstack https://charts.jetstack.io
helm repo update

Instalar CRDs (Custom Resources Definition):
Certificate = solicitar certificados
Issuer = emisor del namespace local
ClusterIssuer =  emisor global
CertificateRequest = solicitud de una firma

"Le dice el api server: ahora existen estos nuevos recursos relacionados con certificados"

kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.18.2/cert-manager.crds.yaml

Montar el cert-manager en un namespace:
helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace
kubectl get po -n cert-manager

Paso 14: Crear un emisor con privilegios de auto-firmado por Kubernetes (CA)
kubectl apply -f issuer.yaml
kubectl get issuer -n security-lab

Paso 15: Crear y desplegar certificado
kubectl apply -f certificate.yaml
kubectl get certificate -n security-lab
kubectl get secret app-tls -n security-lab
kubectl get secret app-tls -n security-lab -o yaml
kubectl get secret app-tls -n security-lab -o jsonpath='{.data.tls\.crt}' | base64 -d

Paso 16: Gestiones de cluster context con Kube Config
kubectl config current-context
kubectl config get-contexts
kubectl config set-context laboratorio --cluster=minikube --user=minikube
kubectl config use-context laboratorio
kubectl config current-context