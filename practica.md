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

Es más intuitivo .....