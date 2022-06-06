# Documentacion vSphere
## Configuración inicial del curso
### Tipos de virtualización de sistemas
* Virtualización servidores
* Virtualizacion Red
* Virtualizacion Almacenamiento
* Virtualizacion Desktop

### Arquitectura tradicional de un centro de datos
Almacenamiento -> Red -> <- Servidor físico 

![servidor](https://i.ytimg.com/vi/emhM7aeLtYw/maxresdefault.jpg)

### Arquitectura virtual de VMware vSphere
Almacenamiento -> Red <- Servidor físico (residen las máquinas virtuales)

![servidorVMware](https://i.ytimg.com/vi/Zo7NQHG6A7s/maxresdefault.jpg)

## El nuevo centro de datos definido por Software

### El uso compartido de los recursos fisicos
Los recursos virtuales entre el numero x máquinas vienen dados por los recursos físicos del servidor.
![Recursos](https://i.ytimg.com/vi/VwZWcbPzx58/maxresdefault.jpg)

### Consumidores de recursos en VMware ESXi
Una maquina virtual es una maquina fisica que convierte los recursos fisicos en archivos. La virtualizacion arregla problemas de actualizaciones de hardware, podemos tener en el mismo servidor físico un servidor de correo o uno web corriendo a la vez sin problemas. Estan aisladas.

![Consumidores](https://i.ytimg.com/vi/lwq_ZABiYuo/maxresdefault.jpg)

### La virtualización de CPU
La virtualizacion de la CPU NO es una emulacion, porque una emulacion permite que los programas se ejecuten en un sistema de servidores que no son para los que se escribieron previamente.
La virtualizacion se ejecuta en forma nativa en procesadores x64.
Cuando muchas maquinas virtuales se ejecutan en un EXI, compiten por los recursos de la CPU. Si nuestro servidor es de 4cores, podemos tener tantas máquinas como la memoria RAM nos permita con 4 cores. El inconveniente sería el tamaño de nuestra memoria.

### La virtualización de memoria
La memoria virtual permite presentar mas memoria a las aplicaciones de la que fisicamente tienen. Podemos tener maquinas virtuales de mas memoria RAM de la que disponemos fisicamente gracias a los ficheros SWAP. Por ejemplo, si tenemos 3 maquinas de 16GB cada una 16x3=48GB y solamente tenemos 32GB, encontramos una diferencia de 16GB que se solucionarán gracias a los ficheros SWAP, convertimos espacio del HDD a Memoria RAM. 

### Introduccion a las redes virtuales
Una maquina virtual puede tener uno o mas adaptadores de red. Se pueden comunicar varias maquinas virtuales gracias al Switch Virtual. Solamente necesitamos una tarjeta de red, pero no es lo recomendado.

### El sistema de archivos en VMware: VMFS
VMFS es un sistema de archivos desarrollado por VMware, dedicado y optimizado para el entorno virtual y el almacenamiento de archivos grandes. La estructura de VMFS permite almacenar los archivos de las VM en una única carpeta, simplificando la administración de las VM.

Ventajas: 
* Los sistemas de archivos tradicionales sólo permiten el acceso en lectura/escritura a un recurso de almacenamiento a un único servidor. VMFS es un sistema de archivos llamado clusterizado, que permite a varios servidores host ESXi acceder de manera simultánea en lectura/escritura a los mismos recursos de almacenamiento. Para asegurar que varios servidores no acceden simultáneamente a la misma máquina virtual, VMFS provee un sistema de bloqueo llamado on disk locking. Éste garantiza que una VM trabaja únicamente con un solo servidor ESX cada vez.

Para gestionar los accesos, ESX utiliza una técnica de reserva SCSI para modificar los archivos de metadatos. Este instante de bloqueo, muy breve, impide a cualquier servidor ESXi y a todas las VM escribir en la LUN. Por este motivo, hay que velar por no realizar reservas SCSI con demasiada frecuencia, lo que podría degradar el rendimiento.

Esta reserva SCSI la realizan los ESXi mediante las siguientes operaciones:

* Creación de un Datastore VMFS.

* Extensión de un Datastore VMFS en un extend.

* Creación de una nueva...

### Los ficheros más importantes de las maquinas virtuales
Los ficheros mas importantes de las maquinas virtuales son los siguientes:
* Fichero configuracion: VM_nombre.vmx
* Fichero swaps: VM_nombre.vswp vmx-VM_nombre.vswp
* Fichero BIOS: VM_nombre.nvram
* Fichero Log: vmware.log
* Fichero plantilla: VM_nombre.vmtx
* Fichero descriptivo disco: VM_nombre.vmdk
* Fichero datos disco: VM_nombre-flat.vmdk
* Fichero raw device map: VM_nombre-rdm(p).vmdk
* Fichero disco snapshot: VM_nombre-######.vmdk
* Fichero datos snapshot: VM_nombre.vmsd
* Fichero estado snapshot: VM_nombre-Snapshot#.fmsn
* Fichero memoria snapshot: VM_nombre-Snapshot#.vmem
* Fichero estado suspend: VM_nombre-*.vmss
* Fichero memoria suspended: VM_nombre-*.vmem

## Creando máquinas virtuales

### El hardware virtual de las maquinas virtuales
Una maquina virtual utiliza hardware virtual. Todas las maquinas virtuales tienen hardware uniforme, esto hace que las maquinas virtuales sean portables.

### Diferentes tipos de discos en maquinas virtuales
* Aprovisionado fino
    * Siempre que puedas elegimos este tipo de disco, debido a que no utiliza todo el espacio que digamos, si no que se reserva dinámicamente.
* Aprovisionado grueso, puesta a cero lenta
    * Este, utiliza todo el espacio del disco que definamos, independientemente de la cantidad de datos usemos. Si creamos un disco de 20gb y usamos 10gb, perdemos 10gb.
* Aprovisionado grueso, puesta a cero rápida
    * Este es igual que el anterior pero tiene que escribir ceros y formatear el disco de manera lenta, este tarda más en provisionarse. 

### Los adaptadores de red de las máquinas virtuales
VM Network -> Red por defecto
Poner el adaptador VMXNET 3 proporciona un 20% de mejora sobre E1000e.

### Beneficios de las VMware Tools
* Instala los drivers de dispositivos
    * SVGA display
    * VMXNET/VMXNET3
    * Balloon driver para gestión de memoria
    * Driver de sincronización de I/O de disco
* Aumentar el rendimiento gráfico
* Aumentar el rendimiento del ratón

Si no instalamos las VMware Tools perdemos rendimiento y eficiencia. 

* Funcionalidades
    * Servicio Heartbeat en el sistema operativo invitado
    * Sincronización de la hora
    * Autentificación del sistema operativo invitado (VMware vCenter Single Sing-on)
    * Posibilidad de apagar máquina virtual sin entrar en su consola

## Gestión centralizada con vCenter Server

### Arquitectura de vCenter Server
Es un servicio que actua como un unico punto de administracion central, se ejecuta en un servidor fisico. Ultima version 6.7 en Windows. vCenter te permite agrupar y administrar recursos de multiples hosts. vCenter Server Appliance es una maquina virtual preconfigurada basada en Linux. 

vCenter es una maquina optimizada que ejecuta vCenter y los servicios asociados, ofrece una alternativa de bajo coste "Appliance" 

### PSC
Puedes instalar PSC y vCenter Server en la misma o en diferente máquina. El vCenter incluye PSC.

El PSC embebido es ideal para entornos pequeños

El PSC embebido no puede unirse a otras instancias de vCenter Server o Platform Services Controller.


