# Instalación agente en servidor Linux derivados de Red Hat

## Compatibilidad
Instana soporta los siguientes sistemas operativos derivados de Red Hat:
- CentOS 6 
- CentOS 7
- CentOS 8
- CentOS 9
- Red Hat Enterprise Linux (RHEL) 6
- Red Hat Enterprise Linux (RHEL) 7 
- Red Hat Enterprise Linux (RHEL) 8
- Red Hat Enterprise Linux (RHEL) 9
- Oracle Enterprise Linux 7
- Oracle Enterprise Linux 8
- Oracle Enterprise Linux 9

> [!NOTE]
> Confirmar otros sistemas operativos soportados en la **[documentación oficial](https://www.ibm.com/docs/en/instana-observability/current?topic=agents-installing-linux#checking-that-you-have-a-supported-operating-system)**

## Requisito

> [!TIP]
>**Verificar conectividad**

>Buscar la URL del backend de Instana, puerto y download_key, estos datos se usaran más adelante:
>
>![image](https://github.com/user-attachments/assets/11df3b33-0f4a-42cf-b94f-a0c391432689)

>```
>telnet ingress-orange-saas.instana.io 443
>curl -v telnet://ingress-orange-saas.instana.io:443
>curl -v -x proxy:port -U "username:password" telnet://ingress-orange-saas.instana.io:443
>```

## Descargar e instalar de agente
En la pantalla de inicio del tenant de Instana, hacer clic en "**Deploy agent**"

![image](https://github.com/user-attachments/assets/8a0c2b7b-2956-44ee-aa79-81195d4c3a5b)

Filtrar bajo la palabra **"Linux"** y seleccionar:

![image](https://github.com/user-attachments/assets/875dfad5-884d-4688-b177-151238109c6c)


Se mostrara la siguiente ventana, seleccionar el modo y la arquitectura requerida, luego descargar el agente

![image](https://github.com/user-attachments/assets/cef31026-63ed-4f46-9b3e-9afed33a96fe)

En la ubicación donde se descargo o copio el instalador, ejecutar la linea de comando:

> [!TIP]
>**Ejecutar comando con permisos sudo o root**
```
rpm -ivh instana-agent-dynamic.x86_64.rpm
```
![image](https://github.com/user-attachments/assets/2d9dc27f-5e61-47d3-b803-c212af8d52ac)

Completar los datos para la conexión del agente de Instana hacia el backend
```
cd /opt/instana/agent/etc/instana
vi com.instana.agent.main.sender.Backend.cfg
```
![image](https://github.com/user-attachments/assets/14f5be59-8aa0-426c-964a-6d684f61ee7f)

Guardar y reiniciar el agente de Instana
```
systemctl restart instana-agent.service
service instana-agent restart
```

> [!NOTE]
> En caso se tenga un entorno con servidor proxy, realizar la **[configuración de proxy en agente Instana](https://github.com/Mainsoft-SA/Instana/blob/main/proxy_agent/readme.md#3-configuraci%C3%B3n-de-proxy-en-agente-instana)**

Luego validar en la consola de Instana:

![image](https://github.com/user-attachments/assets/f9eba09c-1532-41f5-97fe-bd584b871949)

![image](https://github.com/user-attachments/assets/1a980a1e-c921-4f17-9674-0dd02f86203c)


> [!TIP]
> Para una mejor gestión de los recursos a monitorear, se recomienda continuar con la configuración de **[Zonas y Tags](https://github.com/Mainsoft-SA/Instana/blob/main/agente/zona%26tag.md)**
>
> ![image](https://github.com/user-attachments/assets/d4265a10-4698-4c0a-aea9-dd206392193f)


## Iniciar, detener y estado de agente

Iniciar agente
```
systemctl start instana-agent.service
service instana-agent start
```

Detener agente
```
systemctl stop instana-agent.service
service instana-agent stop
```

Estado de agente
```
systemctl status instana-agent.service
service instana-agent status
```

## Desinstalar agente

Derivados de Debian
```
apt list --installed | grep instana-agent
sudo apt-get purge <package_name>
rm -rf /opt/instana/
```

Derivados de Red Hat
```
yum list installed | grep instana-agent
sudo yum remove <package_name>
rm -rf /opt/instana/
```
