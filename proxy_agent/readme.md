# **Configuración de proxy/agente Instana**

Entorno

![image](https://github.com/user-attachments/assets/dd75514b-e611-4f03-8ee6-fe0be82e78be)
SO: Ubuntu 24

1. Proxy Squid

   Instalar Squid
```
   sudo apt install squid
```
   Configurar Squid
```
   nano /etc/squid/squid.conf
```
![image](https://github.com/user-attachments/assets/c5dc3b60-1561-40d4-a632-2a95a557bfe0)

1. Instalación/configuración de agente instana

   Descargar y copiar package
   ![image](https://github.com/user-attachments/assets/af7089f6-3c81-4e8d-abbf-b06491dda956)

   En el servidor proxy, subir y copiar el paquete del agente de Instana
   ```
   scp instana-agent-dynamic.amd64.deb root@10.150.211.198:/root
   ```

   En el servidor del agente, instalar y configurar agente de Instana
   ```
   cd /root
   dpkg -i instana-agent-dynamic.amd64.deb
   nano /opt/instana/agent/etc/mvn-settings.xml
   ```


 



   
