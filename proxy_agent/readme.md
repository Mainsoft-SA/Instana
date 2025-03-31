# **Configuración de proxy/agente Instana**

   Entorno

   ![image](https://github.com/user-attachments/assets/dd75514b-e611-4f03-8ee6-fe0be82e78be)
   SO: Ubuntu 24


## 1. Proxy Squid

   Instalar Squid
   ```
   sudo apt install squid
   ```
   
   Configurar Squid
   ```
   nano /etc/squid/squid.conf
   ```

   Actualizar por la red interna y cambiar las URLs asignadas a su tenant

   ![image](https://github.com/user-attachments/assets/bb983382-4f2c-415a-8349-e978931483f1)

   Reiniciar Squid
   ```
   systemctl restart squid
   ```

## 2. Instalación de agente instana

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

## 3. Configuración de proxy en agente Instana

   Buscar el key
   
   ![image](https://github.com/user-attachments/assets/2647ffa4-eeb8-4d35-b0f8-895ec840834d)

   Configurar archivo mvn-settings.xml y colocar el key
   ```
   Linux: /opt/instana/agent/etc/mvn-settings.xml
   Windows: C:\Program Files\Instana\instana-agent\etc\mvn-settings.xml
   ```
    
   ![image](https://github.com/user-attachments/assets/4dd51455-a327-43ea-af30-f5b2c18f6893)

   Lineas abajo colocar datos del proxy
   
   ![image](https://github.com/user-attachments/assets/1ca7a6d2-6ff9-4b94-99cd-342b1ce46a0d)

   Configurar archivo com.instana.agent.main.sender.Backend.cfg, reemplazar los valores para los parametros host, proxy.host, key y otro que requiera su entorno.

   ```
   Linux: /opt/instana/agent/etc/instana/com.instana.agent.main.sender.Backend.cfg
   Windows: C:\Program Files\Instana\instana-agent\etc\instana\com.instana.agent.main.sender.Backend.cfg
   ```
   
   ![image](https://github.com/user-attachments/assets/5089b0b7-f9fb-4d9a-b971-b9486d3714fa)

   Iniciar agente
   
   ```
   systemctl start instana-agent.service
   ```

   Validar el agente en Instana
   
   ![image](https://github.com/user-attachments/assets/d71967ed-3302-42ff-84f8-9ac91630eb06)


Referencia: https://www.ibm.com/docs/en/instana-observability/current?topic=agents-configuring-host#setting-up-an-agent-proxy

 



   
