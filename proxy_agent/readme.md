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


2. Instalación/configuración de agente instana

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
   ```

   Configurar archivo mvn-settings.xml
   ```
   /opt/instana/agent/etc/mvn-settings.xml
   ```
   
   Colocar el key
   
   ![image](https://github.com/user-attachments/assets/4dd51455-a327-43ea-af30-f5b2c18f6893)

   Colocar datos del proxy
   
   ![image](https://github.com/user-attachments/assets/1ca7a6d2-6ff9-4b94-99cd-342b1ce46a0d)

   Configurar archivo com.instana.agent.main.sender.Backend.cfg

   ```
   nano /opt/instana/agent/etc/instana/com.instana.agent.main.sender.Backend.cfg
   ```
   
   ![image](https://github.com/user-attachments/assets/5089b0b7-f9fb-4d9a-b971-b9486d3714fa)

   Iniciar agente
   
   ```
   systemctl start instana-agent.service
   ```

   Validar el agente en Instana
   
   ![image](https://github.com/user-attachments/assets/d71967ed-3302-42ff-84f8-9ac91630eb06)




 



   
