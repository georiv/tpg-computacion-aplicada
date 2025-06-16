# Trabajo Integrador Grupal

### Integrantes
- Luana Abigail Picone
- Mateo Vega
- Alejandro Ledesma
- Valentina Iacobaccio
- Georgina Cedeño Rivero

  
**Fecha de entrega:** 17/06/2025

## ✅ Punto 1 – Configuración inicial del entorno

### Objetivo:
Configurar el entorno de la máquina virtual (MV): descargar y ensamblar los archivos, blanquear la clave root, cambiarla por "palermo", y establecer el hostname como `TPServer`.

---

### 1.1 Ensamblar los archivos de la MV

- Desde Blackboard se descargaron los archivos `.rar` correspondientes a la MV.
- Se extrajo **solo el primero** con clic derecho → “Extraer aquí”, y automáticamente se ensamblaron todas las partes.
- El resultado fue un archivo `.ova`.
- Al hacer doble clic, se importó automáticamente a **VirtualBox**.

---

### 1.2 Blanquear la clave de root:

Como no se conocía la clave de root, se accedió al modo recovery para cambiarla:

- Se abrió VirtualBox, se seleccionó la máquina virtual y se fue a `Configuración > Sistema > Orden de arranque`.
- Se verificó que el disco duro estuviera primero para evitar problemas con el arranque.
- Se inició la máquina virtual.
- En el menú de GRUB, se presionó la tecla `e` para editar la línea de arranque.
- Se buscó la línea que comienza con `linux /boot/...` y al final se agregó: `init=/bin/bash`.
- Se presionó `Ctrl + X` o `F10` para arrancar con los cambios.
- Se accedió como root al sistema desde una consola minimal.

---

### 1.3. Cambiar la clave de root por "palermo"

- Se remontó el sistema de archivos en modo escritura con el comando `mount -o remount,rw /`.
- Luego se ejecutó `passwd` para cambiar la contraseña.
- Se ingresó la nueva contraseña `palermo` dos veces.
- Finalmente, se reinició el sistema con el comando `exec /sbin/init`.

---

### 1.4. Cambiar el hostname a TPServer

Una vez iniciada normalmente la máquina:

- Se editó el archivo `/etc/hostname` y se reemplazó su contenido por `TPServer`.
- Se editó el archivo `/etc/hosts` y se verificó que contuviera: `127.0.1.1   TPServer`.
- Se aplicaron los cambios con el comando `hostnamectl set-hostname TPServer`.
- Se reinició la máquina para completar el proceso.

---

## ✅ Punto 3 – Configuración de la red con IP estática

### Objetivo
Configurar la interfaz de red de la máquina virtual Debian con una IP estática perteneciente al mismo rango que la red física, incluyendo los parámetros `address`, `netmask` y `gateway`.

---

### 3.1. Verificación del estado de red

- Se utilizó el comando `ip a` en distintos momentos para verificar el nombre de la interfaz de red activa.
- En este caso, la interfaz utilizada fue `enp0s3`.
- Se comprobó si los cambios de configuración surtían efecto tras cada modificación o reinicio.

---

### 3.2. Edición del archivo de configuración

- Se editó el archivo `/etc/network/interfaces` utilizando `nano` y `vim`.
- Se aplicó la siguiente configuración para establecer una IP estática:

  - `allow-hotplug enp0s3`
  - `iface enp0s3 inet static`
  - `address 192.168.1.150`
  - `netmask 255.255.255.0`
  - `gateway 192.168.1.1`

---

### 3.3. Revisión del NetworkManager

- Se verificó el estado del servicio con el comando `systemctl status NetworkManager`.
- No se realizaron modificaciones sobre el servicio ya que no estaba interfiriendo con la configuración manual.

---

### 3.4. Aplicación de cambios

- Para aplicar los cambios sin reiniciar, se ejecutó:

  - `sudo systemctl restart networking`

- También se utilizó:

  - `sudo reboot`

  Para asegurarse de que la configuración se mantuviera tras reiniciar.

---

### 3.5. Verificación post-reinicio

- Nuevamente se utilizó `ip a` para confirmar que la IP `192.168.1.150` fue asignada correctamente a `enp0s3`.

---

### Notas finales

- Se realizaron varios intentos y ediciones hasta lograr una configuración persistente y estable.
- Se eligió una IP fija dentro del mismo rango que el host físico, cuya puerta de enlace es `192.168.1.1`.
- El uso de `reboot` y `restart` sirvió para verificar que los ajustes se conservaban tras cada reinicio del sistema.

---

## ✅ Punto 4 – Agregar disco adicional, particiones y montaje automático

### Objetivo
Agregar un nuevo disco de 10 GB a la máquina virtual y dividirlo en dos particiones:
- `/www_dir` de 3 GB
- `/backup_dir` de 6 GB  
Luego, montar ambas en sus directorios respectivos de forma automática al iniciar el sistema operativo.

---

### 4.1. Agregado del disco en VirtualBox

- Se apagó la VM y desde la configuración de VirtualBox se agregó un nuevo disco virtual de 10 GB (`sdc`) como SATA secundario.

---

### 4.2. Creación de particiones

- Se utilizó la herramienta `fdisk /dev/sdc` para crear dos particiones estándar (tipo 83):
  - `sdc1` de 3 GB → destinada a `/www_dir`
  - `sdc2` de 6 GB → destinada a `/backup_dir`

- Tras crear las particiones, se ejecutó `mkfs.ext4` en cada una para formatearlas con sistema de archivos ext4:
  - `mkfs.ext4 /dev/sdc1`
  - `mkfs.ext4 /dev/sdc2`

---

### 4.3. Creación y montaje de directorios

- Se crearon los directorios:

  - `mkdir /www_dir`
  - `mkdir /backup_dir`

- Luego se montaron las particiones:

  - `mount /dev/sdc1 /www_dir`
  - `mount /dev/sdc2 /backup_dir`

- Se verificó el montaje con `lsblk` y `df -h`.

---

### 4.4. Montaje automático al iniciar el sistema

- Se obtuvo el UUID de las particiones con `blkid`.

- Se editaron las últimas líneas de `/etc/fstab` para incluir:

  - `UUID=xxxxx-xxx-xxx /www_dir ext4 defaults 0 2`
  - `UUID=yyyyy-yyy-yyy /backup_dir ext4 defaults 0 2`

- Se probó el montaje automático con `mount -a` y luego reiniciando.

---

### 4.5. Configurar Apache para `/www_dir`

- Se movió el archivo `index.php` y `logo.png` a `/www_dir`.
- Se editó el archivo `/etc/apache2/sites-available/000-default.conf` para cambiar el `DocumentRoot` a `/www_dir`.
- Se reinició Apache con `systemctl restart apache2`.
- Se verificó desde navegador que la web cargue correctamente desde la nueva ruta.

---

### 4.6. Crear archivo “particion”

- Como `/proc` es un sistema efímero, se creó una copia persistente de su contenido:

  - `cat /proc/partitions > /root/particion`

- Esto permite conservar la información de las particiones incluso después de reiniciar.

---

### Resultado

- Ambos directorios se montan automáticamente al iniciar.
- Apache sirve desde `/www_dir`.
- El contenido de `/proc/partitions` fue guardado según consigna.

