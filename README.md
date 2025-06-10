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

