# Práctica 2 | Despliegue de Aplicaciones Web

M.ª Ángeles Buzón Campaña

## 00. Preparación

![DescripcionImagen][./img/nombreImagen.png]

```bash
# Imagen para la práctica:
# Este comando crea el contenedor con nombre plesk con privilegios.
# También crea un volumen para la persistencia del contenedor
# y define los puertos que se van a usar.
# Por último se indica la imagen a cargar en el contenedor plesk/plesk.
docker run -d --name plesk --privileged --cgroupns=host -v /sys/fs/cgroup:/sys/fs/cgroup:rw -v plesk_data_fresh:/var/lib/plesk -p 666:80 -p 443:443 -p 8880:8880 -p 8443:8443 plesk/plesk

# Volver a levantar el contenedor:
docker start plesk
```



## 01. Despliegue de Plesk
Tardo en levantar el contenedor:
* Tenía abierto otro para un examen anterior del día.
* Tengo que eliminar el que ya había creado de plesk en un intento anterior y lo elimino con `sudo docker container remove plesk`.
* Por conflictos extraños, tengo que cambiar el puerto local `80` a `666` (reflejo esto en el comando de arriba).

Y al principio el navegador no lo muestra, pero es porque se estaba cargando. Puedo cargar la interfaz e iniciar sesión con las credenciales predeterminadas tanto en el puerto `8880` como en el `8443`:
![Inicio sesión en el navegador en el puerto 8880](img/01_puerto8880.png)
![Inicio sesión en el navegador en el puerto 8443](img/01_puerto8443.png)

Si consulto los certificados, se están usando unos que ha creado Plesk automáticamente, pero el navegador sigue interpretando esta página como no segura. En un navegador Chromium puedo ver la info del certificado, que vence casi al mismo tiempo que se crea:
![Certificado de Plesk en Vivaldi](img/01_certificado.png)

**¿Qué ventajas ofrece administrar un servidor web desde Plesk en vez de línea de comandos?**

Su interfaz gráfica es más intuitiva y fácil de usar que la CLI incluso para usuarios expertos y ofrece fácil acceso a secciones específicas para gestionar el servidor en general, automatizar tareas técnicas y optimizar el rendimiento. Además, tiene disponible una gran variedad de plugins para ampliar la funcionalidad.

## 02. Administración básica
Voy a Home > Tools & Settings > Action Log Settings y puedo descargarme un log perteneciente a un periodo de tiempo concreto.
![](img/02_settings.png)

Recibo el archivo `log_from_2025-12-01_to_2025-12-05.txt`, donde hay registros de cuando se creó el servidor Plesk: login, actualización de datos...

```
127.0.0.1 admin [2025-12-04 12:43:37.812] 'Server cloning start' ()
127.0.0.1 admin [2025-12-04 12:43:51.382] 'Update IP Address' ('Interface': 'eth0' => 'eth0', 'IP Address': '172.17.0.3' => '172.17.0.3', 'IP Mask': '255.255.0.0' => '255.255.0.0')
127.0.0.1 admin [2025-12-04 12:44:00.530] 'SSL/TLS certificate on mail server assigned/unassigned' ('SSL Certificate Binding Type': 'MAIL' => 'MAIL', 'SSL Certificate File': 'cert1tLm1d2' => 'scf2m7vuo5poprmeIn1nON', 'SSL Certificate Id': '2' => '3', 'SSL Certificate Name': 'default certificate' => 'Default Certificate')
127.0.0.1 admin [2025-12-04 12:44:02.730] 'Update IP Address' ('Interface': 'eth0' => 'eth0', 'IP Address': '172.17.0.3' => '172.17.0.2', 'IP Mask': '255.255.0.0' => '255.255.0.0')
127.0.0.1 admin [2025-12-04 12:44:26.819] 'Update Administrator Information' ('Client GUID': 'f795477c-d10e-11f0-9b7e-82ce577a02ab' => 'f795477c-d10e-11f0-9b7e-82ce577a02ab', 'Login Name': 'admin' => 'admin', 'Password': '++++++' => '++++++')
127.0.0.1 admin [2025-12-04 12:44:38.110] 'Server cloning complete' ()
127.0.0.1 admin [2025-12-04 12:44:41.084] 'Update Administrator Information' ('Client GUID': 'f795477c-d10e-11f0-9b7e-82ce577a02ab' => 'f795477c-d10e-11f0-9b7e-82ce577a02ab', 'Login Name': 'admin' => 'admin', 'Password': '++++++' => '++++++')
172.17.0.1 admin [2025-12-04 12:52:15.526] 'CP User Login' ('Contact Name': '' => 'Administrator')
172.17.0.1 admin [2025-12-04 13:02:49.450] 'CP User Login' ('Contact Name': '' => 'Administrator')
172.17.0.1 admin [2025-12-05 11:34:20.381] 'CP User Login' ('Contact Name': '' => 'Administrator')
```

(falta lo de los recursos)

**Explica por qué es importante monitorizar estos parámetros.**

