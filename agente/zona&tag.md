# Configuración de Zona y Tags

Es recomendable configurar Zona y Tags para tener un mayor detalle y gestión sobre los recursos monitoreados.

Modificar el archivo *configuration.yaml* ubicado en la siguiente ruta:
```
Linux: /opt/instana/agent/etc/instana
Windows: C:\Program Files\Instana\instana-agent\etc\instana
```
## Zona
Modificar el valor del parametro "availability-zone"

```
com.instana.plugin.generic.hardware:
  enabled: true # disabled by default
  availability-zone: 'DC_Principal'
```
![image](https://github.com/user-attachments/assets/a9fe018e-cfe6-47d0-8fb9-755461999d69)

## Tags
Modificar/agregar parametros del tipo key=value

```
com.instana.plugin.host:
  tags:
    - 'App=Middleware'
    - 'Ambiente=PROD'
    - 'Rol=FrontEnd'
    - 'Gerencia=Operaciones'
    - 'Team_app=Desarrillo'
    - 'Team_infra=Infraestructura'
```

![image](https://github.com/user-attachments/assets/5200209f-976b-4d5d-baa7-ebd56b33ddba)

Desde la vista de Infrastructura se puede observar la zona y tags configurados:

![image](https://github.com/user-attachments/assets/2aa79231-9791-48d3-9f39-d2eb7f10d3fd)
