Cómo instalar cURL en Windows
***

1. En Windows, crea un directorio llamado curl en la unidad C:
![image](https://github.com/Mainsoft-SA/Instana/assets/170142238/ae326fc1-a1ad-4b5d-8ae4-3e83713eb3f3)

2. Abre tu navegador, dirígete a https://curl.se/download.html y descarga uno de los siguientes ficheros dependiendo de si tu Windows es de 64 bits ([curl for 64-bit](https://curl.se/download.html#Win64)) o si tu Windows es de 32 bits ([curl for 32-bit](https://curl.se/download.html#Win32)).
   
3. Una vez descargado, ve al directorio de descargas y descomprime el fichero ZIP.
![image](https://github.com/Mainsoft-SA/Instana/assets/170142238/c6d000ff-b472-45d5-a24e-7a5c98eb02b7)

4. Entra en la carpeta descomprimida y ve dentro del directorio bin, allí tendrás cuatro ficheros (curl.exe, curl-ca-bundle, libcurl-x64.def y libcurl-x64.dll) Cópialos dentro de C:/curl.
![image](https://github.com/Mainsoft-SA/Instana/assets/170142238/7e8bc9fb-2f75-4311-8c35-b8b2e802503d)

5. Una vez esto, tienes que añadir la ruta de CURL en una variable de entorno de Windows para que este comando esté disponible desde cualquier ubicación en el símbolo del sistema. Para ello deberás ir a Inicio y buscar por   "Editar las variables de entorno del sistema":

   ![image](https://github.com/Mainsoft-SA/Instana/assets/170142238/0f67b50a-485f-4954-ad30-3f2909269f52)

6. Se abrirá las Propiedades del sistema, deberás ir a Opciones Avanzadas > Variables de entorno... Seleccionar la variable de entorno Path y dar a Editar...:

   ![image](https://github.com/Mainsoft-SA/Instana/assets/170142238/b3939461-2afb-4d31-8f55-0529157f5e2e)

7. Luego haz clic en Nuevo y añade la ruta del ejecutable de CURL: C:\curl. Luego haz clic en Aceptar para añadir la nueva ruta al path:
![image](https://github.com/Mainsoft-SA/Instana/assets/170142238/64a980e0-40b3-4964-b842-b69c5cef8370)

8. Ahora para comprobar que funciona correctamente, abre un Símbolo del sistema como administrator y ejecuta lo siguiente:
   ```
   curl --version
   ```
   ![image](https://github.com/Mainsoft-SA/Instana/assets/170142238/445c790c-a256-49fa-b885-9e6e822cfd7e)

Referencia: https://help.clouding.io/hc/es/articles/7813911710748-Instalar-cURL-en-Windows
