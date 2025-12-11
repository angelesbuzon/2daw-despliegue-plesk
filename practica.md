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

En las subsecciones `Tools & Resources` y `Server Management` también puedo acceder a información variada sobre los recursos del servidor. Por ejemplo, en `Info and Statistics`:

![Info del servidor](img/02_info.png)

**Explica por qué es importante monitorizar estos parámetros.**

## 03. Instalación de extensiones
Primero instalo **Firewall**, que protege el servidor de ataques controlando el acceso a los servicios mediante la configuración de reglas y políticas.

![Firewall en tienda](img/03_firewall_install.png)

![Info de Firewall](img/03_firewall_config.png)

El sistema de búsqueda no encuentra Fail2Ban (luego me doy cuenta de que está ya integrado en Plesk), así que procedo a instalar **SEO Toolkit**, que ayuda a posicionar la web en los motores de búsqueda; permite hacer auditorías para detectar problemas comunes relacionados con el SEO, analizar archivos de registro y rastrear el rendimiento de las palabras clave.

![SEO Toolkit en tienda](img/03_seo_install.png)

En la configuración no se puede hacer nada porque hay que añadir un dominio primero.

**¿Qué riesgos tiene no contar con un sistema de protección adicional?**

## 04. Creación de dominios
Creo un nuevo dominio (uno que me ofrece Plesk porque no tengo ninguno comprado) y sustituyo el `index.html` creado de forma predeterminada por un `index.php` muy sencillo.

![Añadir dominio](img/04_adddomain.png)

![Añadir dominio 2](img/04_adddomain2.png)

![Editar código del HTML](img/04_phppage.png)

Si intento acceder al dominio, no puedo porque el certificado autofirmado no es seguro.
![Acceso denegado](img/04_accesoweb.png)

Pero al menos aparece como activo en el listado:

![Lista de dominios (uno)](img/04_list.png)

**¿Por qué es útil segmentar webs en vhosts en lugar de alojarlas juntas?**

## 05. Autenticación y control de acceso
Me voy al panel de control de mi dominio `focused-khayyam.172-17-0-2.plesk.page` y luego a la sección `Security > Password-Protected Directories`.

![Acceso a carpetas contraseña](img/05_pass1.png)

Le doy al botón de añadir carpeta con contraseña y añado una llamada `secreto`.

![Carpeta secreto](img/06_pass2.png)

Ahora veamos los jails. Un jail es un conjunto de reglas específico de los configurables en el sistema de prevención de intrusiones de Fail2Ban. Monitoriza logs del servidor para detectar intentos de inicio de sesión fallidos o patrones de acceso maliciosos; si los detecta, automáticamente impide que la dirección IP del supuesto atacante acceda al servidor por un tiempo determinado. Es decir, sirve para proteger el servidor de ataques de fuerza bruta.

Ahora comentamos algunas jails preconfiguradas de Plesk:

1. `plesk-one-week-ban`: bloquea durante una semana direcciones de IP que se hayan baneado manualmente.
2. `plesk-wordpress`: escanea en busca de fallos de autenticación de Wordpress.
3. `ssh`: escanea en busca de fallos de autenticación de SSH.

**¿Por qué son los jails importantes? ¿Se te ocurre algún jail propio?**

## 06. Certificado digital
Antes de nada, instalo las extensiones Let's Encrypt y SSL It!, necesarias para instalar un certificado de este tipo.

Let's Encrypt me dice "Plesk now issues SSL/TLS certificates only via the SSL It! extension", así que me voy a esa otra extensión, que me lista los dominios que tengo y sus certificados. Le doy clic a `focused-khayyam.172-17-0-2.plesk.page` para configurarlo.

![Listado de dominios en SSL It!](img/06_cert1.png)

Entre las opciones disponibles, selecciono `Install a free basic certificate provided by Let's Encrypt` y dejo las opciones predeterminadas; como no tengo dominio propio, me va a dar error siempre, pero dejo constancia de cómo se hace.

![Config certificado](img/06_cert2.png)

**¿Qué riesgos existen si seguimos usando HTTP en lugar de HTTPS?**

## 07. Rendimiento

**¿Qué factores influyen en el rendimiento de un servidor web?**

## 08. Despliegue de aplicaciones

**¿Qué es WordPress? ¿Se usa hoy en día? ¿Tú lo usarías?**