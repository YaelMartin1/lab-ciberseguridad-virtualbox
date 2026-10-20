# Laboratorio de pruebas aislado con VirtualBox y Ubuntu

Trabajo práctico de ciberseguridad: armado de un laboratorio virtual controlado con aislamiento de red, usuario sin privilegios, sistema actualizado, firewall y snapshots de restauración.

📄 Reporte técnico en PDF: Reporte_Técnico_de_Configuración_de_Laboratorio_Yael_Martin.pdf

## Entorno

| Hipervisor | Oracle VirtualBox |
| Sistema invitado | Ubuntu 26.04 Desktop |
| Nombre de la VM | UbuntuLab |
| Recursos | 4 GB de RAM, 4 CPU |
| Red final | Red Interna (`intnet`) |


## 1. Red: NAT y Red Interna

Durante la instalación y las actualizaciones la VM usó NAT, para tener acceso a internet sin quedar visible en la red doméstica.

Una vez terminadas, se cambió a Red Interna para aislarla por completo.


### Justificación técnica

Elegí el modo Red Interna porque, para esta prueba inicial, la máquina virtual no necesita acceso a internet ni comunicación con mi equipo real. Este modo aísla el entorno: la VM solo puede comunicarse con otras máquinas virtuales conectadas a la misma red interna, sin ruta hacia el host ni hacia el exterior. De esta manera se reduce la superficie de ataque y se evita que cualquier software de prueba, malware o tráfico generado en el laboratorio afecte a mi equipo (host) o a otros dispositivos de mi red. No utilicé el modo **Puente (Bridged)** porque conecta la VM directamente a la red doméstica, donde obtiene su propia dirección IP y queda visible y accesible para el resto de los dispositivos, lo que rompe el aislamiento del laboratorio.


## 2. Usuarios con privilegios limitados

Se creó el usuario `practices` con el comando `sudo adduser practices`, separado del usuario administrador `ubuntulab`. Con `id practices` se comprobó que el usuario pertenece solo a los grupos `practices` y `users`, y **no** al grupo `sudo`, por lo que no puede ejecutar tareas administrativas. Con `getent passwd | grep /home` se listaron los usuarios del sistema. Esto aplica el principio de **mínimo privilegio**: si un programa malicioso se ejecuta con esta cuenta, no puede modificar el sistema sin autorización. Por eso las prácticas se realizan con este usuario y no como administrador.


## 3. Permisos en Linux

Comandos ejecutados:

```bash
mkdir lab && cd lab
echo "prueba" > archivo.txt
ls -l
```

La salida de `ls -l` muestra, de izquierda a derecha, el tipo de archivo y los permisos del propietario, del grupo y de otros (r = lectura, w = escritura, x = ejecución), seguidos del propietario, el grupo, el tamaño, la fecha y el nombre.

En este caso, `-rw-rw-r--` indica que el propietario (`ubuntulab`) y el grupo pueden leer y escribir el archivo, y el resto de los usuarios solo puede leerlo. Nadie tiene permiso de ejecución.


## 4. Actualizaciones y hardening

Se actualizó el sistema con `sudo apt update` (actualiza la lista de paquetes disponibles) y `sudo apt upgrade -y` (instala las versiones nuevas). Se usa `sudo` porque estas tareas requieren privilegios de administrador. Los 11 paquetes que aparecen como actualizables (`systemd`, `udev` y sus librerías) corresponden a actualizaciones graduales (*phased updates*): Ubuntu las libera de forma escalonada entre los equipos para detectar problemas antes de distribuirlas a todos, y todavía no están disponibles para esta máquina. Con `apt list --upgradable` se verificó que todos pertenecen a la misma familia de paquetes. Se instalaron todos los paquetes disponibles para este equipo y los restantes se instalarán cuando Ubuntu los habilite.

Además, se activó el firewall con `sudo ufw enable` (la captura muestra "Firewall is active and enabled on system startup"), que bloquea por defecto las conexiones entrantes no autorizadas y reduce la superficie de ataque.


## 5. Snapshots


Se crearon dos instantáneas:

- Instalación Base Limpia: estado del sistema recién instalado, con Guest Additions.
- Clean Install - Hardening applied: estado final, con el sistema actualizado, un usuario sin privilegios (`practices`), el firewall activado y la red en modo Red Interna.

Las instantáneas funcionan como una "máquina del tiempo": si una práctica deja el sistema dañado o comprometido, se puede restaurar la VM a ese punto en segundos.
