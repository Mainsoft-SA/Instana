#############################
### Script de preparación ###
#############################

Ejecutar desde root:

```bash
cat > 1.install_prerequisito.sh << 'OUTER_EOF'
#!/bin/bash
set -e # Abortar inmediatamente si un comando falla

echo "---------------------------------------------------"
echo "Setear nombre de servidor según nro de usuario"
echo "---------------------------------------------------"

while true; do
    read -p "Ingrese número de usuario (ej. 01 o user01): " USER_INPUT
    
    # Sanitización: Extraer solo los dígitos
    USER_ID=$(echo "$USER_INPUT" | tr -dc '0-9')

    if [[ -n "$USER_ID" ]]; then
        # Validar que no sea solo ceros o vacío después de la limpieza
        USER_FMT=$(printf "%02d" $((10#$USER_ID)))
        NEW_HOSTNAME="server-user${USER_FMT}"
        echo "✅ Identificador válido: $NEW_HOSTNAME"
        break # Salir del bucle
    else
        echo "❌ ERROR: '$USER_INPUT' no contiene un identificador numérico válido. Inténtelo de nuevo."
    fi
done

echo "Aplicando cambios de infraestructura..."

# 1. Configurar Hostname de forma persistente
sudo hostnamectl set-hostname "${NEW_HOSTNAME}"

# 1. Preparación del Sistema e instalación de Git
wait_for_dnf() {
    echo "Verificando bloqueo de gestor de paquetes..."
    while sudo fuser /var/lib/rpm/.rpm.lock >/dev/null 2>&1 ; do
        echo "Esperando a que termine otro proceso de actualización..."
        sleep 10
    done
}

# 1. Preparación del Sistema e instalación de Git
wait_for_dnf
# A) Desactivar zchunk en la configuración global
sudo sed -i '$ a zchunk=False' /etc/dnf/dnf.conf

# B) Cambiar metalink por baseurl estática en centos.repo
# Para BaseOS
sudo sed -i '/^\[baseos\]/,/^enabled=1/ s/^metalink=/#metalink=/' /etc/yum.repos.d/centos.repo
sudo sed -i '/^\[baseos\]/a baseurl=https://mirror.stream.centos.org/9-stream/BaseOS/$basearch/os/' /etc/yum.repos.d/centos.repo

# Para AppStream
sudo sed -i '/^\[appstream\]/,/^enabled=1/ s/^metalink=/#metalink=/' /etc/yum.repos.d/centos.repo
sudo sed -i '/^\[appstream\]/a baseurl=https://mirror.stream.centos.org/9-stream/AppStream/$basearch/os/' /etc/yum.repos.d/centos.repo

# Limpieza total de base de datos y metadatos
sudo yum clean all
sudo rm -rf /var/cache/yum

# Reconstrucción de caché e instalación
sudo yum makecache
sudo yum install -y git tar curl

# 2. Instalar K3s con modo de escritura de config habilitado
echo "Instalando K3s..."
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--write-kubeconfig-mode 644" sh -

# 3. Configurar Kubeconfig
mkdir -p ~/.kube
if [ -f /etc/rancher/k3s/k3s.yaml ]; then
    sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
    sudo chown $USER ~/.kube/config
    sudo chmod 600 ~/.kube/config
    export KUBECONFIG=~/.kube/config
else
    echo "ERROR: K3s no se instaló correctamente."
    exit 1
fi

# 4. Configurar /etc/hosts para Instana Core
INSTANA_CORE_IP="194.163.158.27"
cat <<EOF | sudo tee -a /etc/cloud/templates/hosts.redhat.tmpl
# --- Extension para Instana ---
$INSTANA_CORE_IP lab.mainsoft.com
$INSTANA_CORE_IP sandbox-mef.lab.mainsoft.com
$INSTANA_CORE_IP agent-acceptor.lab.mainsoft.com
EOF

cat <<EOF | sudo tee -a /etc/hosts
# --- Extension para Instana ---
$INSTANA_CORE_IP lab.mainsoft.com
$INSTANA_CORE_IP sandbox-mef.lab.mainsoft.com
$INSTANA_CORE_IP agent-acceptor.lab.mainsoft.com
EOF

# 5. Instalar Helm
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh

echo "K3s y Helm listos."

# 1. Instalación silenciosa de dependencias
echo "Instalando dependencias del bash-completion..."
dnf install -y bash-completion > /dev/null

# 2. Bloque de configuración persistente
K8S_CONFIG='
# --- K8s Architecture Suite ---
# Carga core de bash-completion (Previene error _get_comp_words_by_ref)
[ -f /usr/share/bash-completion/bash_completion ] && . /usr/share/bash-completion/bash_completion

# Autocompletado para binarios CNCF
source <(kubectl completion bash)
source <(helm completion bash)

# Alias de alta eficiencia
alias k="kubectl"
complete -o default -F __start_kubectl k
'

# 3. Aplicar persistencia de forma idempotente
if ! grep -q "K8s Architecture Suite" ~/.bashrc; then
    echo "$K8S_CONFIG" >> ~/.bashrc
    echo "Configuración guardada en ~/.bashrc"
fi

OUTER_EOF

chmod +x 1.install_prerequisito.sh
./1.install_prerequisito.sh
sudo reboot
```
#############################
### Instalación de agente Instana en el cluster ###
#############################


Crear archivo values.yaml, reemplazar parametros como Clusterxx, Userxx, Teamxx y correo

```
cat > values.yaml << YAML
cluster:
  name: Cluster00
zone:
  name: Cluster00
agent:
  mode: APM
  pod:
    requests:
      cpu: "500m"
      memory: "768Mi"
    limits:
      cpu: "1500m"
      memory: "1512Mi"
    hostAliases:
      - ip: "194.163.158.27"
        hostnames:
          - "agent-acceptor.lab.mainsoft.com"
    tolerations:
      - operator: "Exists"
  configuration_yaml: |
    # Example of configuration yaml template
    # Host
    com.instana.plugin.host:
      tags:
        - 'Usuario=User00'
        - 'Team=Team00'
        - 'Grupo=0'
        - 'Ambiente=Capacitacion'
        - 'Correo=user00@mainsoft.com.pe'
        - 'Cluster=Cluster00'

    com.instana.plugin.kubernetes:
      pollrate: 120s

    com.instana.plugin.containerd:
      pollrate: 120s
      socket: /run/k3s/containerd/containerd.sock
k8s_sensor:
  deployment:
    enabled: true
    replicas: 1
    pod:
      requests:
        cpu: "120m"
        memory: "128Mi"
      limits:
        cpu: "500m"
        memory: "2048Mi"
YAML
```

Usar como nombre del cluster el numero de usuario asignado (ClusterXX)

<img width="1441" height="760" alt="image" src="https://github.com/user-attachments/assets/f715c2f1-918c-4117-a0c3-bf2f498ee093" />

Agregar **"-f values.yaml"** para aplicar los tag guardados en el paso previo.

```
helm upgrade --install instana-agent \
  --repo https://agents.instana.io/helm \
  --namespace instana-agent \
  --create-namespace \
  --set agent.key='RlWYfpi2RACQ7b4YEfAubg' \
  --set agent.downloadKey='RlWYfpi2RACQ7b4YEfAubg' \
  --set agent.endpointHost='agent-acceptor.lab.mainsoft.com' \
  --set agent.endpointPort=443 \
  --set cluster.name='Cluster00' \
  --set zone.name='Cluster00' \
  -f values.yaml \
  instana-agent

sleep 120
kubectl patch deployment instana-agent-k8sensor -n instana-agent --type='strategic' -p '{
  "spec": {
    "template": {
      "spec": {
        "hostAliases": [{
          "ip": "194.163.158.27",
          "hostnames": ["agent-acceptor.lab.mainsoft.com"]
        }]
      }
    }
  }
}'

kubectl patch deployment instana-agent-controller-manager -n instana-agent --type='strategic' -p '{
  "spec": {
    "template": {
      "spec": {
        "hostAliases": [{
          "ip": "194.163.158.27",
          "hostnames": ["agent-acceptor.lab.mainsoft.com"]
        }]
      }
    }
  }
}'
```

<img width="760" height="440" alt="image" src="https://github.com/user-attachments/assets/e34a7ef7-a149-46e1-809d-47994873838c" />

Validar que todos los pods levanten 

```
kubectl get pods -n instana-agent
```

<img width="858" height="103" alt="image" src="https://github.com/user-attachments/assets/10ed5322-f5b8-489b-a727-34275a526fd6" />


Revisar logs del agente, donde se puede observar que se conecto al backend 

```
kubectl -n instana-agent logs ds/instana-agent | grep lab.mainsoft
```

<img width="1017" height="82" alt="image" src="https://github.com/user-attachments/assets/bc8db1eb-4475-4a28-91ca-f5eed463040d" />


Validar request/limit de los pods

```
kubectl get deployment instana-agent-k8sensor -n instana-agent -o jsonpath='{.spec.template.spec.containers[0].resources}' | jq
kubectl get ds instana-agent -n instana-agent -o jsonpath='{.spec.template.spec.containers[0].resources}' | jq
```

<img width="1520" height="443" alt="image" src="https://github.com/user-attachments/assets/5b440081-6075-4fb9-a60d-bff32db4b5f8" />


Se observa que el agente instalado empieza a reportarse en Instana e iniciar el descubrimiento automatico:

<img width="1867" height="892" alt="image" src="https://github.com/user-attachments/assets/d7a72526-2e91-49e1-897d-510d49dd3564" />

Luego de algunos minutos se observara en el **Infrastructure**

De ser necesario realizar el filtro:
```
entity.zone:ClusterXX
```

<img width="966" height="875" alt="image" src="https://github.com/user-attachments/assets/f3bb075f-c669-4e19-aa01-73992bd27f1a" />


En caso que se requiera desinstalar el agente de Instana:
```
NAMESPACE=instana-agent
kubectl delete agents.instana.io instana-agent -n ${NAMESPACE} --wait
helm uninstall instana-agent -n ${NAMESPACE}
kubectl delete crd agents.instana.io
kubectl delete namespace ${NAMESPACE}
```

https://www.ibm.com/docs/en/iofgs?topic=kubernetes-uninstalling-agent#uninstalling-agents-installed-by-using-the-helm-chart

#############################
### Instalación de Robot Shop ###
#############################

Generar un website con el siguiente formarto: RobotShopXX

<img width="1896" height="323" alt="image" src="https://github.com/user-attachments/assets/d269f0a2-adcb-4acb-aaf2-fe1cc0f33a62" />


<img width="603" height="517" alt="image" src="https://github.com/user-attachments/assets/4345186d-502f-4f95-9df3-6d5e87c45dcc" />

<img width="1337" height="853" alt="image" src="https://github.com/user-attachments/assets/9548a2dd-c0d4-4cec-b3ae-e9c21c69a796" />


Anotar la URL y el key generado, se utilizara en el siguiente Script:


```bash
cat > 2.install_robotshop.sh << 'OUTER_EOF'
#!/bin/bash

# 0. Limpieza de Firewall (Garantiza comunicación entre Pods)
echo "---------------------------------------------------"
echo "Preparando entorno de red..."
if command -v systemctl &> /dev/null; then
    sudo systemctl stop firewalld 2>/dev/null
    sudo systemctl disable firewalld 2>/dev/null
    sudo systemctl mask firewalld 2>/dev/null
    echo "Firewalld desactivado y enmascarado."
else
    echo "Firewalld no detectado o no disponible, saltando..."
fi
echo "---------------------------------------------------"

# 1. Configuración de Instana EUM
echo "---------------------------------------------------"
echo "Configuración de Instana EUM"
echo "---------------------------------------------------"
read -p "Ingrese la URL de Instana: " REPORTING_URL
read -p "Ingrese el EUM_KEY de su aplicación: " EUM_KEY

export REPORTING_URL="$REPORTING_URL"
export EUM_KEY=$EUM_KEY

if [ -z "$REPORTING_URL" ]; then
    echo "Error: La URL no puede estar vacío."
    exit 1
fi

if [ -z "$EUM_KEY" ]; then
    echo "Error: El EUM_KEY no puede estar vacío."
    exit 1
fi

# 2. Preparar Namespace y Repo
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Namespace
metadata:
  name: robot-shop
EOF
#kubectl create namespace robot-shop --dry-run=client -o yaml | kubectl apply -f -
cd ~
rm -rf robot-shop
yum install git -y
git clone https://github.com/instana/robot-shop.git

# 3. Instalación vía Helm
helm upgrade --install robot-shop ./robot-shop/K8s/helm \
  --set eum.key=${EUM_KEY} \
  --set eum.url=${REPORTING_URL} \
  --set redis.storageClassName="local-path" \
  -n robot-shop

# 4. Parche de Recursos Global (Estabilización de CPU/RAM)
for svc in web user catalogue cart; do
    kubectl patch deployment $svc -n robot-shop --type='strategic' -p "{\"spec\":{\"template\":{\"spec\":{\"containers\":[{\"name\":\"$svc\",\"resources\":{\"limits\":{\"cpu\":\"500m\",\"memory\":\"1000Mi\"},\"requests\":{\"cpu\":\"300m\",\"memory\":\"500Mi\"}}}]}}}}"
done

# 5. Parche Crítico para Shipping (Espera al Dump de MySQL)
kubectl patch deployment shipping -n robot-shop --type='strategic' -p '{
  "spec": {
    "template": {
      "spec": {
        "containers": [{
          "name": "shipping",
          "resources": {
            "limits": {"cpu": "1000m", "memory": "1024Mi"},
            "requests": {"cpu": "500m", "memory": "512Mi"}
          },
          "readinessProbe": {
            "initialDelaySeconds": 180,
            "periodSeconds": 20,
            "failureThreshold": 15
          }
        }]
      }
    }
  }
}'

# 6. Configuración de Autoescalado
#kubectl delete hpa --all -n robot-shop 2>/dev/null
#sleep 5
#~/robot-shop/K8s/autoscale.sh

# 7. Ingress (Exposición de la App)
export HOSTNAME=$(hostname)
cat <<EOF | kubectl apply -f -
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: robot-shop-ingress
  namespace: robot-shop
spec:
  rules:
  - host: $HOSTNAME
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web
            port:
              number: 8080
EOF

# 8. Generador de Carga Optimizado
kubectl apply -f ~/robot-shop/K8s/load-deployment.yaml -n robot-shop
kubectl patch deployment load -n robot-shop --type='strategic' -p '{"spec":{"template":{"spec":{"containers":[{"name":"load","resources":{"limits":{"cpu":"200m","memory":"512Mi"},"requests":{"cpu":"100m","memory":"256Mi"}},"env":[{"name":"NUM_CLIENTS","value":"5"}]}]}}}}'

# 9. Parche de EUM (Versión Multi-Pod)
echo "Esperando a que los pods WEB nuevos estén en estado Ready..."
kubectl wait --for=condition=ready pod -l service=web -n robot-shop --timeout=300s
# Obtenemos todos los pods del servicio web y aplicamos el parche a cada uno
echo "Aplicando configuración declarativa de red y EUM..."
sleep 120
kubectl patch deployment web -n robot-shop --type='json' -p='[
  {
    "op": "add",
    "path": "/spec/template/spec/hostAliases",
    "value": [
      {
        "ip": "194.163.158.27",
        "hostnames": ["lab.mainsoft.com", "sandbox-mef.lab.mainsoft.com", "agent-acceptor.lab.mainsoft.com"]
      }
    ]
  },
  {
    "op": "add",
    "path": "/spec/template/spec/containers/0/lifecycle",
    "value": {
      "postStart": {
        "exec": {
          "command": ["/bin/sh", "-c", "sed -i \"s|/eum\\x27)|/eum/\\x27)|g\" /usr/share/nginx/html/eum.html"]
        }
      }
    }
  }
]'

# Validar
# kubectl exec -n robot-shop $(kubectl get pods -n robot-shop -l service=web -o jsonpath='{.items[0].metadata.name}') -- cat /usr/share/nginx/html/eum.html


# 10. Finalización
SERVER_IP=$(curl -s -4 ifconfig.me)
echo "---------------------------------------------------"
echo "Robot Shop instalado y optimizado."
echo "Acceso: http://$SERVER_IP:8080"
echo "---------------------------------------------------"
OUTER_EOF

chmod +x 2.install_robotshop.sh
./2.install_robotshop.sh
```

Esperar que todos los pods levanten (shipping tarda en levantar 15 min)
```
watch kubectl get pods -n robot-shop
```

<img width="574" height="300" alt="image" src="https://github.com/user-attachments/assets/78f7a1a2-0a7a-4045-b502-aa7679d5b48e" />



Registrar en su archivo hosts (C:\Windows\System32\drivers\etc\hosts)
```
194.163.158.27	lab.mainsoft.com
194.163.158.27	sandbox-mef.lab.mainsoft.com 
194.163.158.27	agent-acceptor.lab.mainsoft.com
```

Validar que se ingrese a la URL https://lab.mainsoft.com/eum/eum.min.js 

<img width="1133" height="887" alt="image" src="https://github.com/user-attachments/assets/205e0ec0-6aca-4044-8d2b-ffa41fc77ce0" />

<img width="1283" height="458" alt="image" src="https://github.com/user-attachments/assets/c9cd0f70-6e0d-42c1-839c-5e7a39f372db" />


Luego ingresar a la URL (http://IP:8080)

<img width="961" height="590" alt="image" src="https://github.com/user-attachments/assets/b1c43fa6-b41e-48bd-b3db-622788108446" />



Validar que se este enviando datos a Instana (F12 para ingresar a la herramientes para desarrolladores)

<img width="1820" height="718" alt="image" src="https://github.com/user-attachments/assets/e26bd44f-b2d7-4a71-b6fe-3333886e9117" />


<img width="1904" height="871" alt="image" src="https://github.com/user-attachments/assets/0a3a38e9-1a45-4ed8-8ab0-59de73347b49" />




En caso que se requiera desinstalar robotshop (tarda unos minutos):


```
helm uninstall robot-shop -n robot-shop --kubeconfig=/etc/rancher/k3s/k3s.yaml
kubectl delete deployment load -n robot-shop
kubectl delete namespace robot-shop
```



#############################
### Crear perspectiva de aplicación ###
#############################

<img width="1885" height="896" alt="image" src="https://github.com/user-attachments/assets/f27cef21-f555-416f-add3-fbba432d0cd6" />

<img width="1186" height="708" alt="image" src="https://github.com/user-attachments/assets/897b3ad4-a942-4cc8-acae-7cb4bba0976e" />

<img width="1076" height="636" alt="image" src="https://github.com/user-attachments/assets/bd2fa269-bc87-4c84-abe7-0e3cefac41c2" />

<img width="1071" height="634" alt="image" src="https://github.com/user-attachments/assets/034d9ddf-8c95-4ad8-8614-df8fa2878c17" />

<img width="1894" height="904" alt="image" src="https://github.com/user-attachments/assets/ac931e2a-1a97-476d-8a40-d904ac08f806" />




