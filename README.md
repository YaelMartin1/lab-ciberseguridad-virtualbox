# Laboratorio de pruebas controlado con VirtualBox

## Entorno
- Hipervisor: VirtualBox
- Sistema invitado: Ubuntu 25.04 Desktop
- Nombre de la VM: ubuntuLab
- Recursos: 4 GB de RAM, 2 CPU, 30 GB de disco

## 1. Configuración de red
img/red.jpeg

## 2. Justificación técnica
Elegí el modo Red Interna porque, para esta prueba inicial, la máquina virtual no necesita acceso a internet ni comunicación con mi equipo real. Este modo aísla por completo el entorno: la VM solo puede comunicarse con otras máquinas virtuales conectadas a la misma red interna, sin ninguna ruta hacia el host ni hacia el exterior. De esta manera se reduce la superficie de ataque y se evita que cualquier software de prueba, malware o tráfico generado en el laboratorio afecte a mi equipo o a otros dispositivos. No utilicé el modo Puente (Bridged) porque conecta la VM directamente a mi red doméstica, donde obtiene su propia dirección IP y queda visible y accesible para el resto de los dispositivos (y viceversa). Eso rompe el aislamiento del laboratorio y expone mi red a riesgos innecesarios, algo que debe evitarse en un entorno de pruebas de seguridad.

## 3. Snapshot inicial
img/snapshot.jpeg
