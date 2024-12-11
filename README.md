# Despliegue de aplicaciones web con Plesk
## I. Creación de un grupo de seguridad en AWS
**A.** Confugura las reglas de entrada (abre las puertas):<br>
**1.** Para poder conectarse por SSH y acceder por HTTP/HTTPS<br>
```22``` SSH (TCP)<br>
```80``` HTTP (TCP)<br>
```443``` HTTPS (TCP)<br>
**2.** Para conectar al servidor por FTP<br> Por defecto plesk utiliza _modo activo_ para las conexiones FTP. Para configurar el _modo pasivo_ necesito abrir el rango de puertos<br>
```21``` puerto de control (ambos modos)<br>
```20``` puerto de datos (modo activo)<br>
```49152 - 65535``` rango de puertos dinámicos (modo pasivo)<br>
**3.** Para configurar el servidor de correo electrónico<br>
_De correo saliente:_<br>
```25``` SMTP (TCP)<br>
```465``` SMTPS (TCP)<br>
_De correo entrante:_<br>
```143``` IMAP (TCP)<br>
```993``` IMAPS (TCP)<br>
```110``` POP3 (TCP)<br>
```995``` POP3S (TCP)<br>
**4.** Para utilizar Webhooks de GitHub para hacer pulls y despliegues automáticos en Plesk:<br>
```8443``` (TCP)<br><br>
**B.** Confugura las reglas de salida (abre las puertas):<br>
Permite todo el tráfico de salida para cualquier dirección IP destino (0.0.0.0/0).
## II. Creación de una instancia EC2 en AWS
Crea la instancia con las siguientes características<br>
* Imagen (AMI): Última versión disponible de Ubuntu Server
* Arquitectura: x86
* Tipo de instancia: t2.medium (2 vCPUs, 4 GB de RAM)
* Par de claves: vockey
* Grupo de seguridad: asigna un grupo de seguridad creado en el paso anterior
* Almacenamiento: 30 GB de disco EBS<br>
Despues de crear la instancia asignala una IP elastica.
## III. Instalación de Plesk
Ejecuta un bash script con los siguentes comandos:<br>
```wget https://autoinstall.plesk.com/plesk-installer``` - descarga el script de instalación desatendida de Plesk<br>
```chmod +x plesk-installer``` - asigna permisos de ejecución al script<br>
```./plesk-installer install plesk``` - ejecuta el script de instalación<br>
Para finalizar el proceso de instalación ejecuta el comando ```sudo plesk login``` y ven al formulario de configuración, donde necesitas poner el nombre de correo electrónico del administrador, contraseña del administrador, elegir una licencia.
## VI. Gestión de dominios y subdominios en Plesk
En esta práctica los dominios se gestionan con el DNS wildcard que proporciona el servicio nip.io. 
Este servicio mapea una dirección IP a un nombre de dominio utilizando dos formatos:<br>
**1.** Sin nombre: ```10.0.0.1.nip.io``` se resuelva la IP 10.0.0.1<br>
**2.** Con nombre: ```wordpress.10.0.0.1.nip.io``` se resuelva la IP 10.0.0.1<br>
## V. Despliegue de una aplicación subiendo los archivos desde el panel de control
**1.** Da de alta un dominio con el siguiente formato:
```IP_elástica.nip.io```<br>
Para hacerlo ve a panel de control, ```Websites & Domains``` y haz clic en ```Add Domain```.<br>
Para desplegar un sitio web subiendo los archivos desde nuestra máquina local, selecciona la opción ```Upload files```.
![gen1](/img/gen1.png)
Indica el nombre de dominio.
![gen2](/img/gen2.png)
Para redirigir automáticamente a los usuarios al dominio sin "www", se debe acceder a la sección Hosting & DNS y seleccionar la opción Hosting. En la ventana que aparece indica el ```Preferred domain```
![gen3](/img/gen3.png)
**2.** Para subir los archivos de la aplicación web, se selecciona la opción ```Get Started``` y, dentro de esa sección, se elige ```Upload Files```.
![gen4](/img/gen4.png)
Sube tu archivo html.
![gen5](/img/gen5.png)
Ve al dirección ```IP_elastica.nip.io``` y ve el resultado
![gen6](/img/gen6.png)
**3.** Para añadir el certificado SSL/TSL Let's encrypt ve a ```Websites & Domains```, apartado de tu dominio, selecciona ```Dashboard > Security```, pincha sobre ```SSL/TSL Certificates```, eligue ```Install a free basic certificate```
![gen7](/img/gen7.png)
Y ahora puedes ver que la conexión es segura.
![gen8](/img/gen8.png)
## VI. Despliegue de una aplicación WordPress
Da de alta un dominio con el siguiente formato:
```wordpress.IP_elástica.nip.io```<br>
![wp1](/img/wp1.png)
**1.** Ve a la barra lateral, presiona ```Applications```, eligue ```All available applications```, entra ```Wordpress``` en la barra de busqueda, cuando te sale el resultado presiona ```Install``
![wp2](/img/wp2.png)
**2.** En la ventana de configuración, inserta el nombre de dominio creado y pulsa ```Install```
![wp3](/img/wp3.png)
**3.** Instala el certificado Let's Encrypt.<br>
**4.** Si todo esta hecho correctamente, veras el resultado siguiente
![wp_res](/img/wp_res.png)
## VII. Despliegue de una aplicación web con Git
Pasos preparatorios:<br>
**1.** Haz un fork de repositorio: https://github.com/josejuansanchez/iaw-practica-lamp
**2.** Añade el nuevo subdominio ```git.IP_elástica.nip.io``` y configurar un certificado SSL/TLS de Let’s Encrypt
**3.** Crea una base de datos MySQL y un usuario con permisos sobre la base de datos.<br>
Para hacerlo ve a panel de control del subdominio, pulsa ```Databases```. 
![git1](/img/git1.png)
En la nueva ventana, pulsa ```Add database```. Rellena los campos de formulario y pulsa ```Ok```.
![git2](/img/git2.png)
Ahora veras los opciones de gestion de la base de datos creada:
![git3](/img/git3.png)
Pasos principales:<br>
**4.** Accede a ```Dashboard``` y selecciona la opción ```Git```.
![git4](/img/git4.png)
**5.** Pulsa el boton ```Add repository```. La ventana que aparecerá se utiliza para clonar el repositorio. Plesk permite clonar repositorios remotos y locales, y el clonado de cada repositorio puede realizarse mediante HTTPS o SSH.<br>
Esta práctica contiene instrucciones como hacer el git clone del repositorio remoto por SSH.<br>
Ve a repositorio remoto copía la enlace SSH, y pega en ```Repository URL``` en Plesk.
![git5](/img/git5.png)
El Plesk devolverá una clave pública SSH que hay que añadir en la configuración de tu cuenta de usuario de GitHub para permitir el acceso desde Plesk. Copíala y accede a tu cuenta de GitHub.<br>
Pincha sobre el icóno de tu cuenta y selecciona la sección ```Settings```.
![git6](/img/git6.png)
Procede a ```SSH and GPG keys```.
![git7](/img/git7.png)
Entra el clave SSH de Plesk, el nombre de clave y guardalo.
![git8](/img/git8.png)
**6.** Configura el DocumentRoot, que define el directorio utilizado por el servidor web para servir los archivos de la aplicación. Para ello ve a ```Websites & Domains > Hosting & DNS > Hosting```.
![git9](/img/git9.png)
En el apartado DocumentRoot se debe especificar la ruta a configurar, que en este caso corresponde a la carpeta src del repositorio clonado.
![git10](/img/git10.png)
**7.** Utiliza las deploy actions para ejecutar comandos en el servidor tras cada pull.<br>
Para habilitar el uso de todos los comandos del sistema en las deploy actions, ve a ```Websites & Domains > Hosting & DNS > Hosting``` y configura el acceso SSH como ```/bin/bash```.
![git11](/img/git11.png)
**8.** A continuación, se configurarán las acciones que se ejecutarán automáticamente después de cada pull en las deploy actions. Ve a ```Websites & Domains > Dashboard > Git```
Haz clic en el icono que permite acceder a los ajustes del repositorio clonado.
![git12](/img/git12.png)
**9.** Aquí puedes modificar los valores del archivo config.php, que define la configuración de la base de datos, utilizando los datos creados previamente en Plesk.
![git13](/img/git13.png)
Una vez configuradas las deploy actions, haz clic en el botón ```Deploy now``` para ejecutarlas.
**10.** Configura Webhooks de GitHub para hacer pulls y despliegues automáticos en Plesk.
Ve a ```Websites & Domains > Dashboard > Git```, pulsa el icóno de los ajustes del repositorio.
Aquí se muestra el URL creado por Plesk para crear el Webhook. Copíalo y accede a tu cuenta de GitHub.
![git14](/img/git14.png)
Ve al repositorio y pulsa el boton ```Settings```. Selecciona apartado ```Webhooks```.
![git15](/img/git15.png)
En el formulario que aparecera entra el URL de Webhook y ```Just the push event```.
![git16](/img/git16.png)
## VIII. Configuración de una conexión FTP en FileZilla (modo pasivo)
Da de alta un dominio con el siguiente formato:
```ftp.IP_elástica.nip.io```<br>
**1.** Crea un usuario FTP. Para ello, ve a ```Websites & Domains > Dashboard > FTP```, pulsa sobre el boton ```Add an FTP Account```.
![ftp1](/img/ftp1.png)
Rellena el formulario, guardalo.
![ftp2](/img/ftp2.png)
**2.** Para configurar una conexión FTP en modo pasivo en FileZilla, primero debe ingresar los siguientes parámetros en la pestaña General:
```Protocol:``` FTP - File Transfer Protocol
```Host:``` El nombre de dominio en el formato ftp.IP_ESLÁSTICA.nip.io
```Encryption:``` Require explicit FTP over TLS
```Logon type:``` Ask for password
```User:``` El usuario creado en Plesk
![ftp3](/img/ftp3.png)
**3.** En ```Transfer Settings``` eligue el modo ```Pasive```.
![ftp4](/img/ftp4.png)
**4.** Ahora tienes acceso a los archivos de tu sitio web.
![ftp4](/img/ftp5.png)