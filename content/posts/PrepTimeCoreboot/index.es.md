---
title : 'Pasos previos para coreboot y distros.'
date : 2026-04-05T22:57:55-04:00
tags: ["libreboot","coreboot", "heads",'spi', 'flashprog', "raspberry pi", "pico-serprog", "lbmk", 'alta']
summary: "Pasos previos indispensables para poder instalar coreboot/libreboot/heads."
---

Este es el apartado más grande de todos ya que se tomará en cuenta todo lo necesario
para poder instalar/compilar:

 - Coreboot (desde cero.)
 - Libreboot
 - Heads (Linuxboot)


## Hardware

En el apartado de hardware vamos a necesitar lo siguiente:

- El computador al que le vayas a instalar Coreboot/Libreboot/Heads (En mi caso los Thinkpad X280 y T480, con suerte a expansión en un futuro no muy lejano)

![Thinkpad X280 & T480, both running libreboot](/imgs/x280libreboot/too_much_aura.jpg)

- Otra linux computadora. Y sí, es totalmente necesario ya que de algún lado debemos mandar
las ordenes al Raspberry Pi Pico. La distro realmente no importa, pero es recomendable usar una
base Debian,Fedora ó Arch.
En mi caso hice pruebas con el siguiente hardware: (además de los Thinkpad X280 & T480. Cuando
flahseaba uno utilizaba el otro para el setup de software.)

1. Ideapad S145-14AST con un AMD A9-9425 (y 4GB de RAM a 2400MHz) con Debian 13 XFCE
![2026-02-18-00-14-13-985.jpg](/imgs/x280libreboot/s145st_flashing_1.jpg)

2. Laptop gamer ASUS de un amigo con Debian 13 KDE.
![2026-03-13-12-31-13-354.jpg](/imgs/x280libreboot/gaming_laptop_flash.jpg)


- Raspberry Pi Pico.
Dependiendo de tu raspberry podrías necesitar soldar cables.

Yo recomiendo conseguir el Raspberry Pi Pico H, que ya tiene los pines soldados (o cualquier otro con pines soldados.)

![Raspberry pi pico H](https://cdn-shop.adafruit.com/970x728/5525-02.jpg)

- Desarmador de cruz.

Puedes hacer lo mismo que yo y conseguir:

- Protoboard
- Cables duppont

- Pinza SOIC 8.

![Un clasico](https://tienda.eurocerrajeria.com/57-large_default/pinza-soic-8-pines-pomona.jpg)

Aunque realmente cualquier cosa que te sirva para hacer contacto con el chip de bios
sirve, ya que tambien puedes usar un:

- Un pogo pin del tipo SOP8.
![Soporte para SOP8](https://neven.static.s6.upgates.com/5/563be91dec01e3.jpg)

- O en el caso de que estés bn maniacote así como yo puedes soldar cables dupont directamente al chip de bios. (Que fue lo que hice con mi X280 porque no soporto la pinza soic 8.)
![Lo sigo prefiriendo antes que usar la pinza soic 8](/imgs/x280libreboot/dupont_cables_soldered_directly_to_bios_chip.jpg)

- O bien sí ya estas muy desesperado y necesitas otra entrada en tu top momentos que te mantienen humilde
puedes sujetar los cables directamente con la mano, cinta masking y demasiada fuerza de voluntad.

(El hijo del primo del nieto del sobrino de un amigo me contó que funciona y yo el creo a ese wey.)

![Porque hago estas porquerías](/imgs/x280libreboot/cables_making_contact_with_pure_willpower.jpg)


El punto es, como ya dije, los cables deben hacer contacto con el pin correcto, la forma no importa realmente.

### Circuito y cableado.

Para hacer el cableado del raspberry con la pinza usando el esquema mostrado en la [documentación oficial de lireboot](https://libreboot.org/docs/install/spi.html#wiring)

![pinout rpipico](https://av.libreboot.org/rpi_pico/pinout_serprog.png)
![pinout winbond 8 pins](https://av.libreboot.org/rpi_pico/soic8_pico_pinouts.jpg)

Pero personalmente no logré comprenderlo del todo, por lo que les hice este diagrama:

![mi propio diagrama.](/imgs/x280libreboot/cableado_en_tinkercad_wokwi.png)

Otra cosa a aclarar es que en vez del chip del bios puedes utilizar la pinza o su
soporte que es lo que hice yo para el T480:

![soporte para pinza soic 8](https://www.electrokit.com/resource/u2Bd/u0i/JGvjpUeX0M/quick/3d/14/4e91_41024104.jpg)




## Software en otra Linux computadora.

Para este paso  es recomendable usar una distro basada en debian, pero realmente
puedes usar la distro que quieras y no tener ningún problema (claro, adaptando
los comandos a tu distro de preferencia), pero como yo utilizo Debian 13 (Trixie)
esta guía se basará en eso.

En teoría podríamos buscar todo de manera individual y obtener el mismo resultado, pero 
una de las ventajas del script de automatización de libreboot (lbmk) todos estos pasos 
se vuelven más sencillos (Incluso si no planeas instalar libreboot, solo tendrías que 
remover el directorio cuando finalices.)

## Clonar Repo de LBMK.

```bash
git clone https://codeberg.org/libreboot/lbmk
cd lbmk
```

## Variables de entonrno necesarias.
También es necesario definir una variable de entorno para la cantidad de hilos a usar 
en su compilación. La [documentación oficial menciona:](https://libreboot.org/docs/build/index.html)

```bash
export XBMK_THREADS=2
```

Pero eso dependerá de la cantidad de núcleos/hilos que tenga la computadora que estés usando, 
por lo que para ahorrarte complicaciones y sacar el mayor rendimiento al momento de compilar 
simplemente ejecuta:

```bash
export XBMK_THREADS=$(nproc --all)
```
Lo que hace el comando de arriba es utilizar como valor de *XBMK_THREADS* el resultado que
arroje el comando *nproc --all* y *nproc --all* solo devuelve un entero con el número de
núcleos/hilos disponibles en tu computador.

La [documentación de libreboot](https://libreboot.org/docs/build/index.html) menciona que es necesario ejecutar los siguientes comandos:

```bash
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
```

Pero esto puede llegar a ser un problema en caso de que estés usando tu maquina de desarrollo principal (donde ya tengas tu usuario y correo de gie configurado)
por lo que podrías probar a saltartelo, en mi caso hice la prueba tanto con las variables puestas como con mis credenciales propias y obtuve la misma imagen 
(verificado por sha256sum y comando diff)
Pero es recomendable seguir la guía oficial, por lo tanto otra opción podría ser construirlo en un computador secundario,lxc,contenedor docker,maquina virtual o entorno chroot (que es lo que hice yo), pero al ser un tema más avanzado y amplio queda a cuenta de cada uno el tomar la mejor decisión.
 
### Instalación de dependencias.

Claro, si no como vamos a compilar cosas.
Algo importante a mencionar es que solo hay soporte para distros base:

1. Debian.
2. Fedora.
3. Arch Linux.

(Junto a todos sus derivadas claro está.)

Yo se que a este punto ya parezco disco rayado pero sí. Siguan siempre la documentación oficial (esta guía va mas de apoyo). Bueno pues, habiendo dicho esto, tenemos los [siguientes comandos](https://libreboot.org/docs/build/#first-install-build-dependencies):

``` bash
#./mk dependencies ubuntu #si usas ubuntu
#./mk dependencies fedora43 #para la versión mas reciente de fedora al momento de escribir esto
#./mk dependencies arch # para arch y derivados.
./mk dependencies debian --reinstall
```


### Instalar Flashprog

En el mejor de los casos podríamos hacer algo como:
 
```bash
sudo apt install flashprog
```

Pero de momento el paquete no esta disponible en los repos de debian 13.

Pero en Arch Linux sí que puedes ejecutar:

```bash
sudo pacman -S flashprog
```

y listo.

Ejecutamos:
``` bash
flashprog
```

En nuestra terminal, en vez de tirar un error de comando no existente,
veremos algo como lo siguiente:
![flashprog funcionando](/imgs/x280libreboot/not_readble_bios.png)


Pero si tu distro no tiene el paquete en sus repos tocará seguir estos pasos,
de lo contrario puedes continuar a los siguientes apartados.

Estando dentro del mismo directorio de lbmk ejecutar lo siguiente:

``` bash
./mk -b flashprog
cd src/flashprog
sudo make install
cd -
```

### Explicación de comandos.
Ahora una explicación de que hace cada cosa (para que no creas que te acabo de hackear XD.)

Este comando invoca al script de lbmk para que se encargue de la obtención del código
fuente y compilación de flashprog.
``` bash
./mk -b flashprog
```

Esto nos lleva al directorio del código fuente de flashprog
``` bash
cd src/flashprog
```

Se instala en la raíz de nuestro sistema para poder invocar el comando de **flashprog**
en cualquier lugar en el que nos encontremos

``` bash
sudo make install
```
Finalmente regresamos al directorio raíz de lbmk.

``` bash
cd -
```


## Setup de Raspberry Pi Pico

Indagando por otras guías recuerdo que complicaban mucho esta parte. Pero no debe ser así, ya que podemos simplemente ingresar al siguiente enlace:

[Código fuente de Pico serprog en github](https://github.com/opensensor/pico-serprog)

![Captura de pantalla de la página de github de pico_serprog](/imgs/x280libreboot/pico_serprog_github_page.png)


Ir al apartado de releases y descargar el archivo 
$pico_serprog.elf$

![Captura de pantalla de la página de releases en github de pico_serprog](/imgs/x280libreboot/pico_serprog_releases.png)

En esta parte conecta tu raspberry al computador

![Conexión del raspberry con el computador](/imgs/x280libreboot/conexion_raspberry_pi_pico_con_computadora.jpg)

Ahora abre tu explorador de archivos favorito, en mi caso el default de gnome, nautilus.



y en el apartado de dispositivos  verás algo como si fuera una "memoria usb."

Simplemente arrastra ese $pico_serprog.elf$ al nuevo medio de almacenamiento. y ya.

Como ya viste, el medio de almacenamiento se "desconectó automaticamente" eso es normal, es más, es lo que debería de pasar.


![arrastar el pico_serprog.elf](/imgs/x280libreboot/arrastrar_archivo.png)

si listamos los dispositivos de nuestro computador:
``` bash
ls /dev/ttyACM*
```

Podremos ver que un nuevo dispositivos (muy probablmente) llamada ttyACM0 aparecerá ahí.

Bien, si solo quieres instalar Libreboot/Heads (Linuxboot) esto es todo lo que necesitas.
ya que sus sistemas de automatización de compilación se harán cargo del resto.

Pero si planeas desarrollar para contribuir a los proyectos o compilar coreboot desde cero
entonces los pasos del proximo post te interesan. (Salen mañana con falta, tu confía.)

