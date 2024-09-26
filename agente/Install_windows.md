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

Nota: Confirmar los sistemas operativos soportado en la documentación oficial (https://www.ibm.com/docs/en/instana-observability/current?topic=agents-installing-windows#supported-operating-systems-and-platform-architectures)

En la pantalla de inicio del tenant de Instana, hacer clic en "**Deploy agent**"

![image](https://github.com/user-attachments/assets/8a0c2b7b-2956-44ee-aa79-81195d4c3a5b)

Filtrar bajo la palabra Windows

![image](https://github.com/user-attachments/assets/59b45a62-2299-4c76-93dc-0fe009a42e47)

Se mostrara la siguiente ventana, donde:

1 - Link de descargar el instalador del agente de Instana

2 - Linea de comando para la instalación desatendida del agente de Instana

![image](https://github.com/user-attachments/assets/f62f0d3b-a329-43d8-ba1a-9636f23bc487)

En la ubicación donde se descargo o copio el instalador, ejecutar el comando del punto 2
```
instana-agent-windows-64bit.exe INSTANA_AGENT_ENDPOINT=ingress-orange-saas.instana.io INSTANA_AGENT_ENDPOINT_PORT=443 INSTANA_AGENT_KEY=O_xPXPnaToa9ZpkiPG0Elg INSTANA_DOWNLOAD_KEY=O_xPXPnaToa9ZpkiPG0Elg /quiet
```
![image](https://github.com/user-attachments/assets/a2fdcfdd-7deb-4538-ad71-939acfe2eeea)

En caso se tenga un entorno con un proxy, realizar la siguiente configuración:
https://github.com/Mainsoft-SA/Instana/blob/main/proxy_agent/readme.md#3-configuraci%C3%B3n-de-proxy-en-agente-instana

Luego validar en la consola de Instana:
![image](https://github.com/user-attachments/assets/be53e675-3c89-42fc-8ddc-6f543ab1b8d4)

![image](https://github.com/user-attachments/assets/1a980a1e-c921-4f17-9674-0dd02f86203c)

Para una mejor gestión de los recursos a monitorear, se recomienda continuar con la configuración de **Zonas y Tags**:
https://github.com/Mainsoft-SA/Instana/blob/main/agente/zona%26tag.md

## Iniciar y detener agente

Iniciar servicio
```
net start instana-agent-service
```

Detener servicio
```
net stop instana-agent-service
```
