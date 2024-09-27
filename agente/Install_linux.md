# Instalación agente en servidor Linux

Instana soporta los siguientes sistemas operativos:
- Ubuntu Linux 14.04 (trusty)
- Ubuntu Linux 16.04 (xenial)
- Ubuntu Linux 18.04 (beaver)
- Ubuntu Linux 20.04 (focal)
- Ubuntu Linux 22.04 (jammy)
- CentOS 6 
- CentOS 7
- CentOS 8
- CentOS 9
- Debian 9 (stretch)
- Debian 10 (buster)
- Debian 11 (bullseye)
- SUSE Linux Enterprise Server (SLES) 12
- SUSE Linux Enterprise Server (SLES) 15
- Red Hat Enterprise Linux (RHEL) 6
- Red Hat Enterprise Linux (RHEL) 7 
- Red Hat Enterprise Linux (RHEL) 8
- Red Hat Enterprise Linux (RHEL) 9
- Oracle Enterprise Linux 7
- Oracle Enterprise Linux 8
- Oracle Enterprise Linux 9
- Amazon Linux 1
- Amazon Linux 2
- Alma Linux 8
- Alma Linux 9
- z/Linux, Linux on Z (s390x, 64 bit)
- z/OS (OS/390) - (s390x) - Supported WebSphere and Liberty tracing
- Kylin Linux Advanced Server v10 (x86_64 & aarch64)

> [!NOTE]
> Sistemas operativos soportado en la **[documentación oficial](https://www.ibm.com/docs/en/instana-observability/current?topic=agents-installing-linux#checking-that-you-have-a-supported-operating-system)**

En la pantalla de inicio del tenant de Instana, hacer clic en "**Deploy agent**"

![image](https://github.com/user-attachments/assets/8a0c2b7b-2956-44ee-aa79-81195d4c3a5b)

Filtrar bajo la palabra **"Windows"** y seleccionar:

![image](https://github.com/user-attachments/assets/59b45a62-2299-4c76-93dc-0fe009a42e47)

Se mostrara la siguiente ventana, donde:

1: Link de descargar el instalador del agente de Instana

2: Linea de comando para la instalación desatendida del agente de Instana

> [!IMPORTANT]
>**Extraer el link de descarga y la linea de comando de su propio tenant de Instana**

![image](https://github.com/user-attachments/assets/c325cb5d-5a7d-4708-8257-2714a858a0ca)

> [!TIP]
>**Verificar conectividad, usando la URL de la linea de comando**
```
telnet ingress-orange-saas.instana.io 443
curl -v telnet://ingress-orange-saas.instana.io:443
curl -v -x proxy:port -U "username:password" telnet://ingress-orange-saas.instana.io:443
```


En la ubicación donde se descargo o copio el instalador, ejecutar la linea de comando:

> [!TIP]
>**Ejecutar comando con permisos de administrador**

![image](https://github.com/user-attachments/assets/a2fdcfdd-7deb-4538-ad71-939acfe2eeea)

> [!NOTE]
> En caso se tenga un entorno con servidor proxy, realizar la **[configuración de proxy en agente Instana](https://github.com/Mainsoft-SA/Instana/blob/main/proxy_agent/readme.md#3-configuraci%C3%B3n-de-proxy-en-agente-instana)**

Luego validar en la consola de Instana:

![image](https://github.com/user-attachments/assets/f9eba09c-1532-41f5-97fe-bd584b871949)

![image](https://github.com/user-attachments/assets/1a980a1e-c921-4f17-9674-0dd02f86203c)


> [!TIP]
> Para una mejor gestión de los recursos a monitorear, se recomienda continuar con la configuración de **[Zonas y Tags](https://github.com/Mainsoft-SA/Instana/blob/main/agente/zona%26tag.md)**

![image](https://github.com/user-attachments/assets/d4265a10-4698-4c0a-aea9-dd206392193f)


## Iniciar, detener y estado de agente

Iniciar agente
```
INSTANA_AGENT_FOLDER/bin/start
```

Detener agente
```
INSTANA_AGENT_FOLDER/bin/stop
```

Estado de agente
```
INSTANA_AGENT_FOLDER/bin/status
```

## Desinstalar agente

Derivados de Debian
```
apt list --installed | grep instana-agent
sudo apt-get purge <package_name>
rm -rf /opt/instana/agent
```

Derivados de Red Hat
```
yum list installed | grep instana-agent
sudo yum remove <package_name>
rm -rf /opt/instana/agent
```
