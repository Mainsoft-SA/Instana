# **Monitoreo vSphere**

1. Versiones soportadas

   Soporte confirmado para métricas y datos de configuración para las versiones v6.5, v6.7 y v7.0. Para monitorear vSphere v7.0, el agente Instana debe ejecutarse con JDK 8u242 o superior.

2. Requisitos

   - Agente de Instana previamente instalado
   - Usuario con rol solo lectura como mínimo
   - Conectividad desde el agente hacia el vCenter
   - Solicitar a Instana la habilitación del dashboard vSphere 
  
3. Para habilitar el monitoreo de vSphere, agregar las credenciales de vCenter al archivo de configuración del agente <agent_install_dir>/etc/instana/configuration.yaml
```
  com.instana.plugin.vsphere:
  host: https://<INSERT_VCENTER_URL_HERE>/sdk
  username: <INSERT_USERNAME_HERE>
  password: <INSERT_PASSWORD_HERE>
  enabled: true
  poll_rate: 20 # metrics poll rate in seconds
```

4. Validar el monitoreo desde Instana

   ![image](https://github.com/Mainsoft-SA/Instana/assets/170142238/559d97b7-026a-4122-bff6-c92d6ec536bf)


Documentación IBM: https://www.ibm.com/docs/en/instana-observability/current?topic=instana-monitoring-vsphere
