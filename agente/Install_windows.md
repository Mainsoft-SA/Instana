# Instalación agente en servidor Windows

Instana soporta los siguientes sistemas operativos:
- Windows Server 2022
- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows 11
- Windows 10
- Windows 8.1
- Windows 8

> [!NOTE]
> Sistemas operativos soportado en la **[documentación oficial](https://www.ibm.com/docs/en/instana-observability/current?topic=agents-installing-windows#supported-operating-systems-and-platform-architectures)**

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
> 
> ![image](https://github.com/user-attachments/assets/d4265a10-4698-4c0a-aea9-dd206392193f)


## Iniciar y detener agente de Instana

Iniciar servicio
```
net start instana-agent-service
```

Detener servicio
```
net stop instana-agent-service
```

## Desinstalar agente de Instana

Desde Panel de control -> Agregar o quitar programas se puede desinstalar el agente de Instana

![image](https://github.com/user-attachments/assets/fad2524a-1287-43d0-937e-35d71b16dbde)


