---
title : 'Instalar Libreboot en Thinkpad X280'
date : 2026-04-05T22:57:55-08:00
tags: ["libreboot","coreboot", "lbmk", 'thinkpad','x280','spi', 'flashprog', 'alta']
summary: "Guía a mi estilo para instalar Libreboot en un Thinkpad X280."
---


## Introducción. (Relleno) 🗿
Escribo esta guía e inicio este blog, ya que me hubiera sido de gran utílidad tener algo como esto al momento de instalar coreboot por primera vez en mi Thinkpad X280, si bien la 
[documentación oficial](https://doc.coreboot.org/mainboard/lenovo/skylake.html) cuenta con todo lo necesario para que uno pueda lograr instalar coreboot por su cuenta
(que fué lo que yo hice.)
Lo cierto es que bastantes partes pueden llegar a resultar bastante confusas si es que uno no cuenta con la experiencia necesaria para realizar este tipo de procesos.
(eso sin contar que varias cosas no te las explican y uno se las tiene que "adivinar")
Y no lo voy a negar, en varias ocasiones llegué a brickear mi laptop por no saber bien lo que estaba haciendo.

Bueno, y el tema del blog es tema para otra publicación (si es que llego a escribir acerca de eso, en dicho caso actualizaré acá para que puedan revisar dicha publicación.)

La Thinkpad X280 no es una computadora que estuviera soportada hasta el momento por el proyecto de libreboot, pero gracias al [Pull Request #412](https://codeberg.org/libreboot/lbmk/pulls/412) Puesta en el proyecto oficial de libreboot por su servidor. Ahora es posible instalar libreboot de manera sencilla en este computador.
Espero esta guía le resulte de utildad a alguien.

{{< alert >}}
**Peligro!** Quiero recalcar que este no es un proceso para usuarios principiantes.
{{< /alert >}}

Si bien todo debería salir de manera correcta si sigues esta guía al pie de la letra.
No se toman en cuenta todos los casos habidos y por haber, ya que se asume que el usuario tiene
el criterio suficiente para poder resolver algunas variaciones existentes 
(cómo la instalación de dependencias y herramientas adicionales.)

De igual modo, si no estás totalmente seguro del que se está pasando aquí ni del porqué deberías hacerlo y sigues aquí porque suena interesante, primero, dejame felicitarte por esa gran curiosidad tuya y ganas de aprender, en todo caso el estudiar de pies a cabeza el contenido de este post te será de gran ayuda para aprender cosas nuevas. Igualmente te dejo algúnos recursos interesantes acerca del tema para que puedas aprender más.
(A mí me fueron de mucha ayuda en algún momento)


Un video.
{{< youtube V9-zq4AgsvA >}}

Otro video.
{{< youtube fRpheIAAIz4 >}}


Otro video pq no.
{{< youtube 96MaWUvaG88 >}}

Sorry por no ponerles más cosas, pero no hay mucho en español :(

Habiendo dicho eso. No considero que sea algo realmente difícil con los pasos correctos, en realidad lo más difícil es superar el miedo por malograr el hardware, pero como verán, en caso de algún percance siempre podemos restaurar al bios original.

## Pasos Previos. ⬅️

{{< alert >}}
**¡Atención!** Es OBLIGATORIO seguir los siguientes pasos
{{< /alert >}}


Como noté que muchos pasos se repetían para la instalación de Libreboot/Coreboot/Linuxboot Heads, ya sea para esta máquina o la T480
(para la que también tengo pensado escribir guías) decidí dividirlos en bloques reutilizables, aquí el orden a seguir:

1. [Setup de hardware y software.](/posts/PrepTimeCoreboot/)
2. [Desarmar la maquina.](/posts/X280Dissasembly/)
2. [Hacer backup del bios.](/posts/X280BiosBackup/)


## Generación de ROM. ⛏️

Libreboot es el caso más fácil, pues simplemente es clonar el repo y ejectuar un comando para tener nuestra imagen.

Para ello obviamente clonamos el repositorio oficial de lbmk (sistema de compilación automatizado de libreboot)

### Clonar repositorio. 

``` bash
git clone https://codeberg.org/libreboot/lbmk
cd lbmk
```

### Compilar ROM de Libreboot.

Bien, ahora dentro del mismo directorio del proyecto lbmk hay que ejecutar el siguiente comando:

```bash
./mk -b coreboot x280_vfsp_16mb
```

Como curiosdad, esta parte fue reescrita, inicialmente estaba basada en las notas que dejé en este repo de github:
[X280Libreboot](https://github.com/AlguienSasaki/X280Libreboot)

Pero los desarrolladores ya aceptaron mi pull request así que ya no es necesario (pero se queda para farmear aura xd)

![mi repo](/imgs/x280libreboot/github_X280Libreboot_repo.png)


### Tiempo de compilación. ☕

#### Tiempo en Ideapad S145-14AST. 💻
Y este es el momento para ir por un cafecito o algo así, porque este comando puede llegar a tomar mucho tiempo dependiendo de la computadora que estés utilizando, yo realicé este procedimiento varias veces en 3 computadoras distintas.

1. En un Ideapad S145-14AST con un AMD A9-9425 (y 4GB de RAM a 2400MHz) que le tomó poco más de 2 horas.
Eso es un tiempo estimado, no es exacto
![2026-02-18-00-14-13-985.jpg](/imgs/x280libreboot/s145st_flashing_1.jpg)
![2026-02-17-15-33-55-442.jpg](/imgs/x280libreboot/s145st_flashing_2.jpg)

#### Tiempo en Thinkpad X280. 💻
2. El mismo Thinkpad X280 (i7-8650U con 16GB de RAM) ya con Libreboot, esto lo menciono porque antes de poner mi pull request me olvidé de activar el "Hyper Threading" lo que me dejó con solo los 4 nucleos reales; a la maquina en este estado le tomó una hora y media aproximadamente. Pero al corregir la configuración el tiempo se redujo a una hora aproximadamente.

#### Tiempo en laptop gamer. 🎮
3. Y finalmente en la laptop gamer asus de un amigo. No recuerdo muy bien el modelo, pero tenía un i5 de 11va generación de la serie H, por lo que muy probablemente sería un i5-11400H, a esta computadora  le tomó solo 20 minutos el compilar la imagen (la mayoria fueron solo descargando cosas 💀💀💀)

![2026-03-13-12-31-13-354.jpg](/imgs/x280libreboot/gaming_laptop_flash.jpg)


### Entendiendo que ROM debería flashear. 🤔
Habiendo pasado el tiempo veremos que en la carpeta bin/ se habrá generado una carpeta con el nombre de "x280_vfsp_16mb"

```bash
bin
└── x280_vfsp_16mb
    ├── seabios_x280_vfsp_16mb_libgfxinit_corebootfb.rom
    ├── seabios_x280_vfsp_16mb_libgfxinit_txtmode.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_corebootfb_colemak.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_corebootfb_deqwertz.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_corebootfb_dkqwerty.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_corebootfb_esqwerty.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_corebootfb_frazerty.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_corebootfb_frdvbepo.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_corebootfb_itqwerty.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_corebootfb_noqwerty.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_corebootfb_ptqwerty.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_corebootfb_svenska.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_corebootfb_trqwerty.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_corebootfb_ukdvorak.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_corebootfb_ukqwerty.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_corebootfb_usdvorak.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_corebootfb_usqwerty.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_txtmode_colemak.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_txtmode_deqwertz.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_txtmode_dkqwerty.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_txtmode_esqwerty.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_txtmode_frazerty.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_txtmode_frdvbepo.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_txtmode_itqwerty.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_txtmode_noqwerty.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_txtmode_ptqwerty.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_txtmode_svenska.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_txtmode_trqwerty.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_txtmode_ukdvorak.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_txtmode_ukqwerty.rom
    ├── seagrub_x280_vfsp_16mb_libgfxinit_txtmode_usdvorak.rom
    └── seagrub_x280_vfsp_16mb_libgfxinit_txtmode_usqwerty.rom
2 directories, 32 files
```

Antes de que nos emocionemos al flasear libreboot en nuestro thinkpad x280, es necesario el saber que es lo que hace cada uno de estos archivos.

1. Primero tenemos los prefijos **seabios/seagrub**, esto dependerá de si queremos tanto seabios + grub  como payload o solo seabios, idealmente quisieras ambos, ya que grub permite funciones avanzadas como FDE y seabios es una implementación de bios legacy, aparentemente para esta serie de maquinas (X280,T480s,T480,T580) no se genera la rom automaticamente con u-boot (implementación libre de UEFI) pero es posible agregarlo en caso de requerirse.

2. Luego está el **x280_vfsp_16mb**, esto indica el modelo de placa y el tamaño de chip de bios, como no hay variaciones en este modelo pues no hay que escoger nada, pero en otros como el t440p si que habría que tenerlo en cuenta.

3. **libgfxinit** es el modo de como se inician los graficos, o algo así, realmente no estoy tan seguro, pero lo importante es que tampoco hay que pensarsela demasiado ya que es la  única opción disponible XD.

4. Ahora donde si hay que escoger corebootfb/txtmode, el primero si nos iniciará una interfáz grafica, mientras que la segunda no muestra nada y se va al arranque directamente. Por lo que en la mayoría de los casos querrás escoger corebootfb.

5. Finalmente viene la distribución de teclado como se puede apreciar está disponible colemak,qwerty y dvorak; aparentemente no se debe elegir nada en caso de usar colemak, pero en el caso de las otras 2 podrás ver un prefijo antes del nombre de la distribución de teclado. En caso de tener un teclado en español deberás escoger esqwerty que viene de es + qwerty. Personalmente tengo un teclado en inglés con la distribución qwerty por lo que yo escogería la de la terminación **usqwerty**.

Tremenda explicación, pero considero que es totalmente necesario para entender que estás flasheando y no se te quede la pantalla en negro 
(como amí en alguna ocasión).

Suponiendo que sigues en el directorio raíz del proyecto lbmk, nos movemos a la carpeta con la imagen final de libreboot.

```bash
cd bin/x280_vfsp_16mb
ls
```

y si deberemos ver los mismos archivos .rom que mancionaba arriba:

![ls del directorio](/imgs/x280libreboot/x280_libreboot_available_roms.png)

## Flasheando la ROM de Libreboot. 🥵🔥🔥🔥

{{< alert >}}
**¡Atención!** Recuerda tener tu chip de bios conectado al Raspberry Pi Pico.
{{< /alert >}}

Ahora, finalmente podemos flashear libreboot en nuestro Thinkpad X280
con el siguiente comando:

```bash
#sudo flashprog -p serprog:dev=/dev/ttyACM0 -w seagrub_x280_vfsp_16mb_libgfxinit_corebootfb_esqwerty.rom #En caso de tener el teclado en español.
sudo flashprog -p serprog:dev=/dev/ttyACM0 -w seagrub_x280_vfsp_16mb_libgfxinit_corebootfb_usqwerty.rom
```

Claro está que debes escoger la rom que acomode a tus necesidades.
Este paso toma alrededor de unos 10 minutos, pero puede tardar menos en caso de la longitud de tus cables y todo eso que ya explique arriba. y como advertencia.

{{< alert >}}
**¡Atención!** No toques la pinza ni el raspberry, es más ni lo mires feo o se vaya a desconectar.
{{< /alert >}}


Si todo salió bien deberíamos ver una pantalla como la siguiente:

![Correct internal flash](/imgs/x280libreboot/correct_internal_flash.png)

Lo restante es solo quitar cuidadosamente la pinza del chip de bios del x280 y volverla a ensamblar que es lo mismo que hicimos para desarmarla, pero alreves.

Y si ahora intentamos encender el Thinkpad, parpadearán las luces y se apagará, esto es normal porque el bios está reconociendo todo el hardware, por lo que tenle paciencia y reintentalo unas 3 o 4 veces.

Pero cuando se decida a prender veremos lo siguiente:

![x280libreboot.jpg](/imgs/x280libreboot/x280libreboot.jpg)

Ya quedó. Ahhhh!!!!!
![Gato con ojos de rayos.](https://media.tenor.com/dYDlDrsJhsUAAAAM/tongue-cat-tongue-out.gif)

## Conclusiones. (Aún más relleno) 🗿

Si haz llegado hasta aquí, felicitaciones, el modificar un computador de esta manera es algo que muy poca gente
se atreve a hacer (ya sea por miedo a causar daños irreparables o ver pocas ventajas a simple vista)

La realidad es que en nuestro mundo moderno, se nos ha acostumbrado a tratar estas maquinas como cajas magicas
y que debemos aceptar que por debajo se ejecuten capas abstractas que no sabemos que hacen ni a quien rinden cuentas.
A pesar de que no me gustaría sonar como paranoico, al no ser auditable el código, no podemos asumir que se nos
tiene vigilados en todo momento. (Capaz que sí, o capaz que no, quien sabe jajaj)

Al reemplazar el firmware propietario por  algo como libreboot (o cualquier distro de coreboot realmente)
rompemos con esta dinamica (aunque sea un poco, pero eso no se puede negar)
Representado algo tan simple que debería de ser obvio, pero tan alejado aparentemente: La soberanía sobre
algo que tú mismo compraste, es decir tienes el control de tu maquina y esta solo te obedece a tí, siendo
tuya y de nadie más.

Claro que no es perfecto, pues aún requerimos del microcode propietario de intel para poder tener un sistema funcional 
(es posible removerlo pero el sistema se volvería inestable.)
y de otros blobs binarios propietarios para poder encender la maquina en primer lugar como el ifd,ime 
(intel management engine, incluso doblemente neutrailzado con deguard + me_cleaner),gbe; pero es importante 
no evitar que eso nos desanime/haga pensar que es inutil/no sirve 
(no permitas que la perfección sea la enemiga de algo bueno) 
Ya que no hace falta saberse de memoria que hace cada línea en binario/ensamblador/C o la arquitectura del
procesador para poder ver la belleza en todo esto.

Nadie (o almenos eso creo) comprende su hardware y software en un 100%. Lo bonito del software lbre no es que tu
tengas/debas auditar absolutamente todo por tu cuenta, si no que cualquiera al que se le de la gana tenga el derecho
y la posibilidad de hacerlo. 

Finalmente, el computador (por mas viejo que sea) enciendo, corre en base a código auditable por una comnunidad giantesca
y deja de depender casi en su totalidad de las cadenas impuestas por el fabricante para funcionar.
Eso ya justifica todo el esfuerzo puesto.

Recuerda que:

> El software libre siempre vale la pena, la libertad no tiene precio.
