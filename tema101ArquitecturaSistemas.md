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

* **/proc**: está montado en la RAM que el Kernel usa durante el arranque.
    * *cpuinfo*: muestra las diferentes CPUs de la máquina y sus carteríisticas propias. Las flags son las características que podemos des/activar.
    * *interrupts*: vemos la tabla de interrupciones. La gestión es bastante automatizada hoy en día.
    * *ioports*: muestra las diferentes direcciones pci, sus entradas y salidas y los procesos que están en ellos.
    * *dma*: veremos los canales de acceso directo a memoria que hay en la máquina.
* **/sys**: está montado en la RAM que el Kernel usa durante el arranque. Mismas funciones de /proc pero solo tiene la información de los disp. relacionados con el hardware. Es menos de consulta de usuario.
* **/dev**: contiene ficheros asociados a disp. de almacenamiento. Tiene su propia nomenclatura para gestionar los disp.
* **/etc/udev/reules.d**:
