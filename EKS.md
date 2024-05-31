# **Instalación de agente en EKS (AWS)**

1. Desde el Instana -> Agentes -> Install Agents -> Filtrar por EKS

  ![image](https://github.com/Mainsoft-SA/Instana/assets/170142238/78ee857f-b225-47fc-992f-f1409d933bd6)
  ![image](https://github.com/Mainsoft-SA/Instana/assets/170142238/f3cad36d-d2be-460d-bdeb-027f16224a73)

2. Completar el nombre del cluster y la zona (seleccionar o crear), luego descargar el deployment.yaml

  ![image](https://github.com/Mainsoft-SA/Instana/assets/170142238/12464257-9eb5-40d0-9ea1-495993c36272)

3. Copiar y ejecutar el deployment.yaml
  ```
  kubectl apply -f deployment.yaml
  ```

  Documentación IBM: https://www.ibm.com/docs/en/instana-observability/current?topic=iha-installing-host-agent-amazon-elastic-kubernetes-service-eks
