# Filtrar nodos en kubernetes

Preparar archivo instana-agent-values.yaml

```
cat > instana-agent-values.yaml << YAML
agent:
  pod:
    nodeSelector:
      instana-monitored: "true"
YAML
```


Para aplicar en la configuración del agente (agregar upgrade y instana-agent-values.yaml):

```
helm upgrade --install instana-agent \
   --repo https://agents.instana.io/helm \
   --namespace instana-agent \
   --create-namespace \
   --set agent.key=O_xPXPnaToa9ZpkiPG0Elg \
   --set agent.downloadKey=O_xPXPnaToa9ZpkiPG0Elg \
   --set agent.endpointHost=ingress-orange-saas.instana.io \
   --set agent.endpointPort=443 \
   --set cluster.name='Mainsoft' \
   --set zone.name='Mainsoft' \
   --values instana-agent-values.yaml \
   instana-agent
```

Para etiquetar los nodos:

```
kubectl label nodes itzvsi0-0g06korj itzvsi1-0g06korj instana-monitored=true --overwrite
```

Para retirar etiqueta de los nodos:

```
kubectl label nodes itzvsi2-0g06korj itzvsi3-0g06korj instana-monitored-
```

Para validar:

```
kubectl get nodes
watch kubectl get pods -n instana-agent -o wide
```

<img width="1238" height="212" alt="image" src="https://github.com/user-attachments/assets/fd4a9631-2e67-4921-bcfa-3bec45e33951" />

