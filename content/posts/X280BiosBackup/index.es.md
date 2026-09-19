---
title : 'Respaldar BIOS en Thinkpad X280'
date : 2026-04-05T22:57:55-06:00
tags: ["flashprog", 'thinkpad','x280','spi', "backup", 'moderado']
summary: "Guía rapida de como respaldar el bios en un Thinkpad X280."
---


Para este punto es importante haber desarmado la maquina, ubicado correctamente el chip de bios y 
haber instalado flashprog en otra computadora con linux. Estos pasos se detallan en estos posts:

1. [Instalar flashprog - Prep Time for coreboot](/posts/PrepTimeCoreboot/#instalar-flashprog)
2. [Desarmar Thinkpad X280](/posts/X280Dissasembly)

## Localización del chip de bios.
Ya con la maquina totalmente desenergizada podemos proceder a hacer maldades con ella >:V

<center><img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTvmX2d3YECEi-37eP4lO8QNAL1dhxKiXvNZE91jUevXzsR5MExLiOBVM8&s=10" alt="muehehe >:3" width="200"></center>

lo primero sería el conectar el chip del bios con el raspberry pi pico y verificar que este funcione.

El chip de bios en cuestión se encuentra en medio del procesador y el puerto WWAN. No bromeo cuando digo que para darme una idea de donde estaba el chip, tuve que entrar a un foro ruso donde tenían fotos de varios modelos de Thinkpad y su localización del bios (tremendos heroes sin capa.) Igual encontré el post de [este usuario de reddit.](https://www.reddit.com/r/thinkpad/comments/m0f32y/thinkpad_x280_bios_chip/) 

![x280bios.jpg](/imgs/x280libreboot/x280bios.jpg)

## Conectar el bios a la pinza soic 8 para que la reconozca el raspberry.

Ahora viene el paso más dificil... 
bueno, realmente no es taaaan difícil, sino estresante (almenos para mí).

Si siguieron la guía del cableado de libreboot y dependiendo de su modelo de pinza esto puede variar, pero en mi caso el cable rojo va alineado con el pin 1. Y el pin 1 se identifica por el pequeño punto que tiene el chip en la esquina

![2026-02-17-08-35-00-063.jpg](/imgs/x280libreboot/x280bioschip.jpg)

Ten mucho cuidado al abrir la pinza, pues hay unas pequeñas resistencias a los lados que podrían llegar a romperse si no se tiene cuidado. Con eso en mente, se debería de ver algo así.

![x280biosspi.jpg](/imgs/x280libreboot/x280biosspi.jpg)

Y ahora ya podemos probar que nuestro cableado sea el correcto. Para ello hay que volver 
a nuestro otro computador corriendo linux.

## Detección correcta del chip de BIOS.

Si en el ejecutamos:

``` bash
sudo flashprog -p serprog:dev=/dev/ttyACM0
```

Deberíamos ver que se reconce el chip tal y como se muestra en la siguiente imagen.

![2026-02-17-13-40-43-279.jpg](/imgs/x280libreboot/valid_bios_chip.jpg)

En caso de que aparezca un mensaje como el siguiente:
![not_readble_bios.png](/imgs/x280libreboot/not_readble_bios.png)

Tocará ser pacientes y volver a intentar conectar el chip de bios con la pinza o 
recablear el circuito hasta que el chip se reconozca. Como ya mencioné anteriormente, 
este es uno de los pasos mas dificiles, personalmente me tomó como 12 intentos y 
varias verificaciones del circuito para que se me reconociera, así que no desesperen.

Ya habiendo pasado lo mas difícil, recomiendo ni siquiera ver feo a la maquina para 
que no se vaya a desconectar jajaj. Y es precisamente en este momento cuando viene 
la diversión. 

### Hacer el backup ahora 🪑

Lo primero es en la computadora linux externa, escribir el siguiente comando:

```bash
sudo flashprog -p serprog:dev=/dev/ttyACM0 -r x280_original_bios_1.bin
```

Como ya debería ser obvio, puedes cambiar el nombre de **x280_original_bios_1.bin**
por el que quieras. Este comando puede llegar a tardar más o menos dependiendo de 
varios factores, como el largo de los cables que hayas usado, como se puede apreciar en mi caso
, el largo de los cables de la pinza a mi raspberry es de aproximadamente unos 
30 cm y mi cable usb es de 1m, por lo que el hacer el backup me tomó unos 10 
minutos aproximadamente, pero tu puedes ser suertudo y que te tome menos.

Ya habiendo terminado este tiempo, es necesario volver a ejecutar el comando pero diferente (¿?):

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


{{< alert >}}
**Atención!** Guarda muy bien estos archivos!!!
{{< /alert >}}

Ya que en caso de que algo llegase a salir mal (que si sigues esta guia al pie de la letra y tienes un poco de cuidado y sentido común no debería pasar.) no podrás restablecer el bios original.

## Lo arruiné, como vuelvo al bios original.

En caso de que algo haya salido mal (moviste la pinza, compilaste mal la imagen de coreboot, etc.) 
Hay que repetir los pasos de este mismo post.

 - Desde [Localizar el chip de bios](#localización-del-chip-de-bios) 

![Conexión con chip de bios](/imgs/x280libreboot/x280biosspi.jpg)

 - Hasta [Detección correcta del chip de BIOS](#detección-correcta-del-chip-de-bios)

![Flashprog detecta correctamente el chip de bios](/imgs/x280libreboot/valid_bios_chip.jpg)


Ahora, tomamos en backup del chip de bios que hicimos en el paso:
[Hacer el backup](#hacer-el-backup-ahora-)
y que claramente no olvidaste realizar.

 - Ejecutaremos el siguiente comando:

```bash
sudo flashprog -p serprog:dev=/dev/ttyACM0 -w x280_original_bios_1.bin
```

Recordando que **x280_original_bios_1.bin** es el nombre que le hayas puesto.

 - Esperar un rato.

 - Volver a ensamblar la máquina.

 - Encenderla.

 - Listo

![Esta imagen no es mía, nunca tomé una foto del bios stock, creditos a su autor](https://auctions.afimg.jp/p1115526142/ya/image/p1115526142.1.jpg)

