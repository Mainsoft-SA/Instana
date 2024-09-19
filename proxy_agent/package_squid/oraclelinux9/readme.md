# Descarga de paquetes para la instalación del proxy Squid en Oracle Linux 9

1. Se debe descargar el paquete de Squid desde un servidor Oracle Linux, con salida a Internet, y repositorio por defecto configurado:

   ```
   sudo dnf download --resolve squid
   ```

   ![image](https://github.com/user-attachments/assets/1102eb71-2a19-45c2-96d0-82fd01ed05af)


2. Transferir los paquetes descargados al servidor Squid Proxy que debe tener habilitado el gestor de paquetes dnf.

   En el siguiente link se lista los paquetes descargados en un ambiente de pruebas:
   https://github.com/Mainsoft-SA/Instana/tree/main/proxy_agent/package_squid/oraclelinux9

   Nota: Se sugiere la descarga de los paquetes como se detalla en el punto 1 en caso se presente algún inconveniente.

3. Instalación de los paquetes en el servidor Squid Proxy, usando dnf o rpm:

   ```
   cd /home/user/squid_install/
   sudo dnf install ./*.rpm
   ```

   ![image](https://github.com/user-attachments/assets/cf107eaf-8497-490e-a0cd-973087c0af68)
   ![image](https://github.com/user-attachments/assets/31d200a7-e5a6-4222-8ff3-3c3d3e1117fc)
   ![image](https://github.com/user-attachments/assets/0201b94d-7009-4364-b601-5cc546358f91)

4. Habilitar e iniciar el servicio de Squid:

   ```
   sudo systemctl start squid
   sudo systemctl enable squid
   sudo systemctl status squid
   ```
   ![image](https://github.com/user-attachments/assets/b2408763-d000-4050-afd1-0d783260974a)

5. Continuar la configuración del Squid detallado en el siguiente link:
   https://github.com/Mainsoft-SA/Instana/blob/main/proxy_agent/readme.md

   
