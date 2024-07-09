# TEMA 101: ARQUITECTURA DE SISTEMA  

## Configuración de las características Hardware.

### Activación de dispositivos

Linux es capaz de detectar todos los dispositivos hardware que existen en los equipos informáticos.  
El primer programa o aplicación que se encarga de hacer la detección y configuración más básica de los disp. para después ser detectados por el SO es la BIOS o más moderno UEFI.  
Son firmware que se encuentran embebidos dentro de chips en la MB.  
Detectan, hacen un diagnóstico básico funcional de todo lo conectado y lo ponen ene stado funcional para que el SO los detecte.  
BIOS hacía una gestión de los componentes hasta mediados de los 2000, que empezó a cambiar a UEFI que tiene unas caracteristicas más sofisticadas que BIOS.  
Normalmente para entrar en la BIOS o UEFI se suele usar Supr, F12 o F2 dependiente de la marca del equipo.  
Podemos hacer varias cosas dentro de este firmware:
* Activar o desactivar **periféricos integrados**. Por ejemplo, una de las prácticas por cuestiones de mantimiento o tiempo, al fallar un componente se accede a BIOS y se desactiva.
* **Protección ante errores básicos**.
* **IRQ**. Tabla de interrupciones. Caracterísitcas que permite realizar señales de interrupción frente a eventos de teclado o ratón que son preferente dentro del ciclo de vida del PC.
* **DMA**. Acceso directos a memoria. Permiten relaicionar diversos componentes de hardware para que puedan acceder a diferentes sectores dentro de la RAM del sistema.
Antiguamente tenía más sentido, con las mejoras de los años se ha optimizaado y no es necesario tocarlo, pero si por ejemplo tenemos un server en el que hemos instalado una RAM específica que permite una mayor tasa de ratio de transferencia dei información y en la BIOS está en la velociad estándar podemos modificar los valores a los que indica el fabicante de la RAM. También se modifican las caracterísitcas de la CPU.
Dentro de la BIOS/UEFI podemos indicar qué disco será el que arranque el SO.

### Inspección de dispositivos en Linux

* **lspci**: nos muestra todos los dispositivos que se encuentran conectados al bus pci, contraldores de discos, tarjetas gráficas o de sonido... Es una lista en la que encontramos, en la 1ª columna la dirección del disp., en la segunda el tipo de disp. y en la tercera la marca y modelo del disp. No sinteresa saber el Kernel driver in use y el modules, ésteúltimo nos indica otro driver que podríausarse si fallase el primero.
    * -s dirección: muestra solo dispositivos en la dirección especificada
    * -v: para ver más nivel de detalle
    * -k: nos muestra los detalles SOLO de los drivers.
* **lsusb**: lista todos los dips. usb conectados la máquina, teclados, disco extraibles... Vemos en la primera columna el Bus de datos que funciona, en la segunda el dispositivo, en la tercera la marca del dips. y en la anterior a esta la dirección del disp. Para Linux cada Bus de datos puede tener varios puertos usb.
    * -v: para ver más nivel de detalle
    * -d dirección: muestra solo dispositivos en la dirección especificada
    * -t: muestra un diagrama en forma de árbol que contiene todos los buses, puentes, dispositivos y conexiones entre ellos.
* **lsmod**: lista los módulos o drivers que están cargados en ese momento en diferentes columnas. Muestra el nombre del módulo, el tamaño que ocupa en la RAM en bytes y si está siendo usado o no por otro módulo. En caso de estár siendo usado por otro módulo, primero hay que desactivar aquellos que estén usando otros.
* **modprobe**: se encarga de gestionar los módulos.
    * -r nombre_modulo: elimina un módulo del sistema. Si los módulos de los que depende también están sin usar, intentará eliminarlos también.
* **modinfo**: muestra las caracterísitcas de un driver/módulo que podemos añadir o quitar.
    * -p módulo: no mostrará las caracterísitcas y el tipo de dato que le podemos dar del módulo indicado.
En el directorio /etc/modprobe.d/ tenemos los ficheros de cada módulo con las caracterísitcas que le podemos cargar. Aquí podremos cambiarlas y hacerlo persistente.
Si hay algún módulo dándonos problemas podemos incluirlo en un blacklist. Existen diferentes ficheros para ello ya que están por fabricante. Dentro pondremos "blacklist nombre_modulo".

### Ficheros de información y ficheros de dispositivos

* **/proc**: está montado en la RAM que el Kernel usa durante el arranque. Ficheros interesantes:
    * *cpuinfo*: muestra las diferentes CPUs de la máquina y sus carteríisticas propias. Las *flags* son las características que podemos des/activar.
    * *interrupts*: vemos la tabla de interrupciones (IRQ). La gestión es bastante automatizada hoy en día.
    * *ioports*: muestra las diferentes direcciones pci, sus entradas y salidas y los procesos que están en ellos.
    * *dma*: veremos los canales de acceso directo a memoria que hay en la máquina.
* **/sys**: está montado en la RAM que el Kernel usa durante el arranque. Mismas funciones de /proc pero solo tiene la información de los disp. relacionados con el hardware. Es menos de consulta de usuario.
* **/dev**: contiene ficheros asociados a disp. de almacenamiento. Tiene su propia nomenclatura para gestionar los disp, HD[A | B | C | D] en función del puerto IDE que estuviera conectado. Actualmente indiferentemente del tipo de disco duro la nomenclatura es /dev/sd/X.
* **/etc/udev/reules.d**: pseudosistema que es el encargado de identificar todos los dispositivos cold/hot plug instalados en el sistema. Depende del directorio /sys y actúa como un demonio que escucha el directorio /sys y ante cualqueir cambio de evento en ese directorio, con sus reglas predefinidas, gestionarlo.

### Dispostivos de almacenamiento

-normativa 7DEV
* Canales IDE: /dev/hdX
* Floppy disks: /dev/fdX
* Canales SCSI (SATA, SDD, USB): /dev/sdX
* Canales SD Cards: /dev/mmcblkXpY
* Canales NVM: /dev/nvmeZnXpY

## Arranque del sistema

### BIOS o UEFI

* **BIOS**: app que se almacena en una memoria volátil dentro de la placa base que se suele llamar *firmware*. Objetivos:
    * Checar que todos los componentes hardware de la máquina estén funcionando correctamente en su forma más báscia.
    * Buscar el primer stage del cargador de arranque y pasarle la ejecución a ese primer sector de arranque para que comience la carga del SO.
BIOS asume que en los primeros 440 Bytes del primer dispositivo de almacenamiento que nosotros tengamos configurado como arranque, es dónde se encuentra el primer stage del cargador de arranque, normalmente se llama *MBR*. Este *MBR* debe tener 2 cosas:
    * El primer stage del cargador de arranque.
    * La tabla de particiones del disco para poder acceder a las diferentes particiones y buscar el sistema de ficheros raíz del SO Linux.
Pasos de arranque.
![pasosArranqueBIOS](https://github.com/eguirod/linux/assets/71733548/84fdea89-dea6-4285-9d3a-f5053c01c197)  
* **UEFI**: es diferente a BIOS. El firmware es cápaz de detectar particiones y de leer el sistema de ficheros. Ventajas, ya no depende del MBR y mejora la seguridad. Usa una memoria volátil NVRAM que está incluida en la placa base. Empezó a emplearse a finales de los 2000. En este sistema existen las apps EFI que se pueden ejecutar de forma automática o pueden ser llamadas desde un menú de arranque. Lo más habitual son los bootloaders, pero también existen los selectores de SO, herramientas de diagnóstico, de reparación o DVDs virtuales para arracar LIVEs CD de SO. Esta info debe estar en una partición de almacenamiento con un sistema de fichero compatibles (FAT32 (más usado), FAT16 o FAT12). En el disco duro que esté el SO debe existir la EFI System Partition, que contiene todas esas apps. Lo ideal es que esté instalado a parte del SO.
![pasosArranqueUEFI](https://github.com/eguirod/linux/assets/71733548/d02360f6-d6bd-4005-88ca-0feb855874a6)  

### Cargador de arranque  

El ¨**gestor de arranque** se va a encargar del segundo stage del cargador de arranque con el objetivo de poder arrancar el kernel y el SO e inicializar el sistema. El más famoso es **GRUB**, que es un software que lo que permite es una vez que llega al segundo stage del cargador de arranque, ejecuta esta app y nos muestra un listado de los diferentes SO que tengamso en el HDD para poder cargar e inicializar. Suele aparecer en modo silencioso, y para verlo se usa la tecla *Shift* o *Esc*. También peude cambiar el comportamiento del arranque gracias a sus opciones y si queremos que tengan persistencia esos cambios podemos modificarlas en `/etc/default/grub`. Algunas opciones:
* `init`: establece un iniciador alternartivo (*systemd*).
* `sytemd.unit`: se indica la unidad (target) a usar por systemd. `systemd.unit=graphical.target`.
* `mem`: lacantidad de RAM a usar.
* `maxcpus`: la cantidad de CPU a usar.
* `root`: establece en qué partición está el raíz.
* `rootflags`: parámetro para montar la partición raíz.
* `ro`:solo lectura para raíz.
* `rw`: lectura escritura para raíz.
Todas estas opciones las incluiríamos en la línea `GRUB_CMDLINE_LINUX`.
