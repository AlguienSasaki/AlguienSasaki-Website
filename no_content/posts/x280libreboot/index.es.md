---
title : 'Como instalar Libreboot/Coreboot en el Thinkpad X280'
date : 2026-04-05T22:57:55-06:00
tags: ["libreboot","coreboot",'thinkpad','x280','spi', 'flashprog', 'moderado']
summary: "Proceso inncecesariamente detallado de como instalar tanto libreboot como coreboot en el thiknpad x280."
---


Escribo esta guía e inicio este blog, ya que me hubiera sido de gran utílidad tener algo como esto al momento de instalar coreboot por primera vez en mi Thinkpad X280, si bien la 
[documentación oficial de coreboot](https://doc.coreboot.org/mainboard/lenovo/skylake.html) cuenta con todo lo necesario para que uno pueda lograr instalar coreboot por su cuenta
(que fué lo que yo hice.)
Lo cierto es que bastantes partes pueden llegar a resultar bastante confusas si es que uno no cuenta con la experiencia necesaria para realizar este tipo de procesos.
(Y no lo voy a negar, en varias ocasiones llegué a brickear mi laptop por no saber bien lo que estaba haciendo.)

Bueno, y el blog es tema para otra publicación (si es que llego a escribir acerca de eso, en dicho caso actualizaré acá para que puedan revisar dicha publicación.)

La Thinkpad X280 no es una computadora que estuviera soportada hasta el momento por el proyecto de libreboot, pero gracias al [Pull Request #412](https://codeberg.org/libreboot/lbmk/pulls/412) En el proyecto oficial de libreboot. Ahora es posible instalar libreboot de manera sencilla en este computador. La idea inicial era hacer un artículo gigante donde mostrara el proceso tanto de la instalación de Libreboot cómo de Coreboot, pero al ver que son 2 cosas muy diferentes y me estaba quedando un artículo de +6,000 palabras ps me decidí a que lo mejor era separarlo en 3 partes.

{{< alert >}}
**Warning!** Quiero recalcar que este no es un proceso para usuarios principiantes.
{{< /alert >}}

Si bien todo debería salir de manera correcta si sigues esta guía al pie de la letra.
No se toman en cuenta todos los casos habidos y por haber, ya que se asume que el usuario tiene
el criterio suficiente para poder resolver algunas variaciones existentes 
(cómo la instalación de dependencias e instalación de programas.)


De igual modo, si no estás totalmente de que se está haciendo y el porqué y solo decides hacerlo porque suena interesante, primero dejame felicitarte por la gran curiosidad tuya y esas ganas de aprender, en todo caso el estudiar de pies a cabeza el contenido de este post te será de gran ayuda para aprender cosas nuevas. Igualmente te dejo algúnos recursos interesantes acerca del tema para que puedas aprender más.


Un video.
{{< youtube V9-zq4AgsvA >}}


Otro video.
{{< youtube fRpheIAAIz4 >}}


Otro video pq no.
{{< youtube 96MaWUvaG88 >}}

Sorry por no ponerles más cosas, pero no hay mucho en español :(

Habiendo dicho eso. No considero que sea algo realmente difícil con los pasos correctos, en realidad lo más difícil es superar el miedo por malograr el hardware, pero como verán, en caso de algún percance siempre podemos restaurar al bios original.

## Hardware

En el apartado de hardware vamos a necesitar lo siguiente:

- Nuestro Thinkpad x280 (ps sino donde vamos a instalar coreboot)
- Otra linux computadora. Y sí, es totalmente necesario ya que de algún lado debemos mandar la orden al programador. 
- Pinza SOIC 8.
- Desarmador de cruz.
- Raspberry Pi Pico ó CH341A.

Cabe recalcar que la [documentación oficial de libreboot](https://libreboot.org/docs/install/spi.html#do-not-buy-ch341a)
desaconseja en su totalidad el hacer uso de este dispositivo, ya que dependiendo de donde lo compres puede que te toque
una unidad mal diseñada y no haga un correcta gestión del voltaje, lo que podría ocasionar que acabes quemando tu chip
de bios.
En mi caso compré ambos dispositivos para probarlos, y efectivamente, el CH341A tenía el mismo error,


Dependiendo de tu raspberry podrías necesitar soldar cables.

Yo recomiendo conseguir el Raspberry Pi Pico H, que ya tiene los pines soldados (o cualquier otro con pines soldados.)

Y puedes hacer lo mismo que yo y conseguir:

- una protoboard
- cables

Para hacer el cableado del raspberry con la pinza usando el esquema mostrado en la [documentación oficial de lireboot](https://libreboot.org/docs/install/spi.html#wiring)

Ahora pasamos al computador para programar el raspberry pi Pico.

## External Linux Computer

* transicion a la compu * 

Bien, es recomendable usar una distro basada en debian

Estando en la compu, hay x cosas importantes que hacer, así que presten atención.
 
- Instalar Flashprog para hacer backup
- Programar el raspberry para que actúe como un escritor de firmware
- Hacer un backup de nuestro bios.

Lo mas recomendable es utilizar una distro basada en debian (como ubuntu, mint, kali, etc.)

### Instalar Flashprog

Por el momento no es posible simplemente hacer:
 
```bash
sudo apt install flashprog
```

ya que el paquete no está disponible en los repos de debian 13.

por lo que hay que compilar desde el código fuente.
Primero debemos instalar las dependencias necesarias para evitar errores:

e instalar las dependencias necesarias para poder compilar:

``` bash
sudo apt install git build-essential pciutils-dev libpci-dev libftdi1-dev libusb-1.0-0-dev libjaylink-dev libgpiod-dev pkg-config
```

``` bash
git clone https://review.sourcearcade.org
cd flashprog
make
sudo make install
```

y sí ejecutamos 
``` bash
flashprog
```

En nuestra terminal, en vez de tirar un error veremos algo como lo siguiente:

![](imgs/x280libreboot/flashprog_log.png)

ahora podemos pasar a programar el raspberry.


### Raspberry Pi Pico

Indagando por otras guías recuerdo que complicaban mucho esta parte. Pero no debe ser así, ya que podemos simplemente ingresar al siguiente enlace:

[Pico serprog](https://github.com/opensensor/pico-serprog)

Ir al apartado de releases y descargar el archivo 
$pico_serprog.elf$

En esta parte conecta tu raspberry al computador

* mostrar un video conectando el raspberry a la compu *

Ahora abre tu explorador de archivos favorito, en mi caso el default de gnome, nautilus.

y en el apartado de dispositivos  verás algo como si fuera una "memoria usb."

Simplemente arrastra ese $pico_serprog.elf$ al nuevo medio de almacenamiento. y ya.

Como ya viste, el medio de almacenamiento se "desconectó automaticamente" eso es normal, es más, es lo que debería de pasar.

si listamos los dispositivos de nuestro computador:
``` bash
ls /dev/
```

podremos ver que un nuevo dispositivos (muy probablmente) llamada ttyACM0 aparecerá ahí * insertar sonidos de explosión mientras se hace zoom al dispositivo*

Ese es el raspberry y eso es todo. Pero para probarlo debemos desarmar la compu.

## Dissasembly the X280

Volvemos a la camara uhhhh.

Recordando tener la maquina totalmente apagada * muestra algun video apagando la maquina, cerrandola y dandole vuelta. *


Ahora debemos quitar los 5 tornillos visibles de la carcasa, la verdad no se que tamaño de destornillador usar, pero el que te perimta quitarlos sirve

![x280bottom.jpg](/imgs/x280libreboot/x280bottom.jpg)
 
Ya con la maquina abierta es indispensable el retirar cualquier tipo de energía del sistema, tal y como lo indica la [documentación de coreboot](https://doc.coreboot.org/mainboard/lenovo/skylake.html#:~:text=Disconnect/remove%20all%20batteries%20(and%20CMOS%20battery%20if%20equipped).)

Para quitar la bateria es necesario desatornillar los 5 tornillos que la mantienen en su lugar, puedes usar el desarmador que usaste para abrir la tapa del computador.
![x280pcb.jpg](/imgs/x280libreboot/x280pcb.jpg)

![x280battery.jpg](/imgs/x280libreboot/x280battery.jpg)

Para quitar el cmos es necesario quitar la bateria principal, ahora simplemente jalar el conector y listo.

![x280cmos.jpg](/imgs/x280libreboot/x280cmos.jpg)

## Respaldo del bios original.

### Localización del chip de bios.
Ya con la maquina totalmente desenergizada podemos pasar a hacerle maldades a la maquina >:V, lo primero sería el conectar el chip del bios con el raspberry pi pico y verificar que este funcione.

El chip de bios en cuestión se encuentra en esta parte (señalala pe) en medio del procesador y el puerto WWAN. No bromeo cuando digo que para darme una idea de donde estaba el chip, tuve que entrar a un foro ruso donde tenían fotos de varios modelos de Thinkpad y su localización del bios (tremendos heroes.) Pero decidí confiar en [este usuario de reddit](https://www.reddit.com/r/thinkpad/comments/m0f32y/thinkpad_x280_bios_chip/) y efectivamente.

![x280bios.jpg](/imgs/x280libreboot/x280bios.jpg)

### Conectar el bios a la pinza soic 8 para que la reconozca el raspberry.

Ahora viene uno de los pasos mas difíciles, bueno, realmente no es taaaan difícil, sino estresante.

Si siguieron la guía del cableado de libreboot y dependiendo de su modelo de pinza esto puede variar, pero en mi caso el cable rojo va alineado con el pin 1. Y el pin 1 se identifica por el pequeño punto que tiene el chip en la esquina

![2026-02-17-08-35-00-063.jpg](/imgs/x280libreboot/x280bioschip.jpg)

Ten mucho cuidado al abrir la pinza, pues hay unas pequeñas resistencias a los lados que podrían llegar a romperse si no se tiene cuidado. Con eso en mente, se debería de ver algo así.

![x280biosspi.jpg](/imgs/x280libreboot/x280biosspi.jpg)

Y ahora ya podemos probar que nuestro cableado sea el correcto. Para ello hay que volver a nuestro otro computador corriendo linux.

Si en el ejecutamos:

``` bash
sudo flashprog -p serprog:dev=/dev/ttyACM0
```

Deberíamos ver que se reconce el chip tal y como se muestra en la siguiente imagen.

![2026-02-17-13-40-43-279.jpg](/imgs/x280libreboot/valid_bios_chip.jpg)

En caso de que aparezca un mensaje como el siguiente:
![not_readble_bios.png](/imgs/x280libreboot/not_readble_bios.png)

Tocará ser pacientes y volver a intentar conectar el chip de bios con la pinza o recablear el circuito hasta que el chip se reconozca.
Como ya mencioné anteriormente, este es uno de los pasos mas dificiles, personalmente me tomó como 12 intentos y varias verificaciones del circuito para que se me reconociera, así que no desesperen.

Ya habiendo pasado lo mas difícil, recomiendo ni siquiera ver feo a la maquina para que no se vaya a desconectar jajaj.
Y es precisamente en este momento cuando viene la diversión. 

### Hacer el backup ahora 🪑

Lo primero es en la computadora linux externa, escribir el siguiente comando:

```bash
sudo flashprog -p serprog:dev=/dev/ttyACM0 -r x280_original_bios_1.bin
```

Como ya debería ser obvio, puedes cambiar el nombre de $x280_original_bios_1.bin$ por el que quieras. Este comando puede llegar a tardar más o menos dependiendo de varios factores, como el largo de los cables que hayas usado, como se puede apreciar en mi caso, el largo de los cables de la pinza a mi raspberry es de aproximadamente unos 30 cm y mi cable usb es de 1m, por lo que el hacer el backup me tomó unos 10 minutos aproximadamente, pero tu puedes ser suertudo y que te tome menos.

Ya habiendo terminado este tiempo, es necesario volver a ejecutar el comando pero diferente (?):

```bash
sudo flashprog -p serprog:dev=/dev/ttyACM0 -r x280_original_bios_2.bin
```

Esto para verificar que no se haya movido accidentalmente la pinza y leyó un bit incorrecto y sí, eso lo cambia todo, ya que si despues intentas restaurarlo, ps no podrás y habrás brickeado tu Thinkpad X280.

Personalmente recomiendo el desconectar la pinza y volver a ejecutar:

```bash
sudo flashprog -p serprog:dev=/dev/ttyACM0 -r x280_original_bios_3.bin
```

y

```bash
sudo flashprog -p serprog:dev=/dev/ttyACM0 -r x280_original_bios_4.bin
```

Pero si no te sientes muy comodo haciendo eso, o el lograr que la compu reconozca el chip del bios, puedes hacer solo los 2 backups.

Suponiendo que el directorio donde hiciste tus backups está vacio, puedes ejectuar el siguiente comando:


```bash
#Otras opciones del comando:
#diff x280*
#diff *.bin
diff *
```

y si el comando no devuelve nada, entonces nuestro backup ha sido exitoso. Ahora 

> [!NOTE]
> GUARDA MUY BIEN ESOS ARCHIVOS!!!!

Ya que en caso de que algo llegase a salir mal (que si sigues esta guia al pie de la letra y tienes un poco de cuidado y sentido común no debería pasar.) no podrás restablecer el bios original.

