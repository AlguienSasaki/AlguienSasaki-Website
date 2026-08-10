
## Libreboot.
Libreboot es el caso más fácil, pues simplemente es clonar el repo y ejectuar un comando para tener nuestra imagen.
Para ello clonamos el repositorio oficial de lbmk

``` bash
git clone https://codeberg.org/libreboot/lbmk
cd lbmk
```

y definir una variable de entorno para la cantidad de hilos a usar en su compilación.
La [documentación oficial menciona:](https://libreboot.org/docs/build/index.html)
```bash
export XBMK_THREADS=2
```

Pero eso dependerá de la cantidad de hilos que tenga la computadora que estés usando para instalar libreboot, por lo que para ahorrarte complicaciones y sacar el mayor rendimiento de tu computadora al momento de compilar simplemente ejecuta:

```bash
export XBMK_THREADS=$(nproc)
```

La [documentación de libreboot](https://libreboot.org/docs/build/index.html) menciona que es necesario ejecutar los siguientes comandos:

```bash
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
```

Pero esto puede llegar a ser un problema en caso de que estés usando tu maquina principal donde ya tengas tu usuario y correo de git configurado para hacer este proceso de instalar libreboot, por lo que podrías llegar a saltartelo si hace falta, en mi caso hice la prueba tanto con las variables puestas como con mis credenciales propias y obtuve la misma imagen, pero es recomendable seguir la guía oficial, por lo tanto otra opción podría ser construirlo en un entorno chroot, que es lo que hice yo, pero al ser un tema más avanzado queda a cuenta de cada uno el investigarlo.
 
Ya habiendo puesto todas estas variables, es necesario antes de compilar la imagen el instalar las dependencias. 
Como seguro ya lo habia mencionado, solo hay soporte para distros base:

1. Debian.
2. Fedora.
3. Arch Linux.

(Y todos sus derivados claro está.)
Y no se que a este punto ya parezco disco rayado pero sí. Siguan siempre la documentación oficial. Bueno pues, habiendo dicho esto, tenemos los [siguientes comandos](https://libreboot.org/docs/build/#first-install-build-dependencies):

``` bash
#./mk dependencies ubuntu #si usas ubuntu
#./mk dependencies fedora43 #para la versión mas reciente de fedora al momento de escribir esto
#./mk dependencies arch # para arch y derivados.
./mk dependencies debian --reinstall
```

Como yo utilizo debian 13 (Trixie) pongo el comando de arriba, pero dependerá de tu distribución.
Otra cosa a recalcar es que en realidad puedes compilar/instalar libreboot en cualquier distribución de linux, pero deberás instalar las dependencias necesarias para tu distro de manera externa o crear un script específico para tu distro. (Que de hacerlo aprovecha y contribuye al proyecto en codeberg)

Y ahora ya viene lo bueno, y para dar algo de contexto; tuve que reescribir esta parte, ya que inicialmente el port a Libreboot no era oficial, lo hice yo mismo (sí) y estaba escribiendo la guía en base a un repositorio en mi cuenta de github.
[X280Libreboot](https://github.com/AlguienSasaki/X280Libreboot)

Pero los desarrolladores ya aceptaron mi pull request así que ya no es necesario seguir los pasos que mostré ahí, solo lo menciono como curiosidad (y para farmear aura xd)

![mi repo](/imgs/x280libreboot/github_X280Libreboot_repo.png)

Bien, ahora dentro del mismo directorio del proyecto lbmk hay que ejecutar el siguiente comando:

```bash
./mk -b coreboot x280_vfsp_16mb
```

Y este es el momento para ir por un cafecito o algo así, porque este comando puede llegar a tomar mucho tiempo dependiendo de la computadora que estés utilizando, yo realicé este procedimiento varias veces en 3 computadoras distintas.

1. En un Ideapad S145-14AST que le tomó poco más de 2 horas.
![2026-02-18-00-14-13-985.jpg](/imgs/x280libreboot/s145st_flashing_1.jpg)
2026-02-17-15-33-55-442.jpg
![2026-02-17-15-33-55-442.jpg](/imgs/x280libreboot/s145st_flashing_2.jpg)

2. El mismo Thinkpad X280 ya con Libreboot, esto lo menciono porque antes de poner mi pull request me olvidé de olvidar el "Hyper Threading" que en resumen me dejó con solo 4 hilos a la maquina en este estado le tomó una hora y media aproximadamente. Pero al corregir la configuración el tiempo se redujo drasticamente a unos 40 minutos aproximadamente.

3. Y finalmente en la laptop gamer asus de un amigo. No recuerdo muy bien el modelo, pero tenía un i5 de 11va generación de la serie H, por lo que muy probablemente sería un i5-11400H, a esta computadora  le tomó solo 20 minutos el compilar la imagen (la mayoria fueron solo descargando cosas 💀💀💀)

![2026-03-13-12-31-13-354.jpg](/imgs/x280libreboot/gaming_laptop_flash.jpg)

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

* insertar el yata de shikanoko xd *

Antes de que nos emocionemos al flasear libreboot en nuestro thinkpad x280, es necesario el saber que es lo que hace cada uno de estos archivos.

1. Primero tenemos las palabras seabios/seagrub, esto dependerá de si queremos tanto seabios + grub  como payload o solo seabios.

2. Luego está el x280_vfsp_16mb, esto indica el modelo de la placa madre y el tipo de chip de bios, como no hay variaciones en este modelo pues no hay que escoger nada, pero en otros como el t440p si que habría que tenerlo en cuenta.

3. libgfxinit es el modo de como se inician los graficos, o algo así, realmente no estoy tan seguro, pero lo importante es que tampoco hay que pensarse la demasiado.

4. Ahora donde si hay que escoger corebootfb/txtmode, el primero si nos iniciará una interfáz grafica y mostrará letras, mientras que la segunda no muestra nada y se va al arranque directamente. Por lo que en la mayoría de los casos querrás escoger corebootfb.

5. Finalmente viene la distribución de teclado como se puede apreciar está disponible colemak,qwerty y dvorak; aparentemente no se debe elegir nada en caso de usar colemak, pero en el caso de las otras 2 podrás ver un prefijo antes del nombre de la distribución de teclado. En caso de tener un teclado en español deberás escoger esqwerty que viene de es + qwerty. Personalmente tengo un teclado en inglés con la distribución qwerty por lo que yo escogería la de la terminación usqwerty.

Tremenda explicación, pero considero que es totalmente necesario para entender que estás flasheando y no se te quede la pantalla en negro.

Suponiendo que sigues en el directorio raíz del proyecto lbmk, nos movemos a la carpeta con la imagen final de libreboot.

```bash
cd bin/x280_vfsp_16mb
ls
```

y si deberemos ver los mismos archivos .rom que mancionaba arriba:

![ls del directorio](/imgs/x280libreboot/x280_libreboot_available_roms.png)

Ahora, finalmente podemos flashear libreboot en nuestro Thinkpad X280
con el siguiente comando:

```bash
#sudo flashprog -p serprog:dev=/dev/ttyACM0 -w seagrub_x280_vfsp_16mb_libgfxinit_corebootfb_esqwerty.rom #En caso de tener el teclado en español.
sudo flashprog -p serprog:dev=/dev/ttyACM0 -w seagrub_x280_vfsp_16mb_libgfxinit_corebootfb_usqwerty.rom
```

Claro está que debes escoger la rom que acomode a tus necesidades.
Este paso toma alrededor de unos 10 minutos, pero puede tardar menos en caso de la longitud de tus cables y todo eso que ya explique arriba. y como advertencia.

> No toques la pinza ni el raspberry, es más ni lo mires feo o se vaya a desconectar.

* insertar emoji que siempre pone el agate del gato con ojos de trueno xd *

Si todo salió bien deberíamos ver una pantalla como la siguiente:

* Poner aca la termianl cuando ya se flasheo*

Lo restante es solo quitar cuidadosamente la pinza del chip de bios del x280 y volverla a ensamblar que es lo mismo que hicimos para desarmarla, pero alreves.

* Mostrar pasos reensamblandola en camara rapida jsjsj *

Y si ahora intentamos encender el Thinkpad, parpadearán las luces y se apagará, esto es normal porque el bios está reconociendo todo el hardware, por lo que tenle paciencia y reintentalo unas 3 o 4 veces.

Pero cuando se decida a prender veremos lo siguiente:

![x280libreboot.jpg](/imgs/x280libreboot/x280libreboot.jpg)

* insertar emoji que siempre pone el agate del gato con ojos de trueno otravez xd y muchas explosiones.*

> Ahora debo escribir una conclusión diciendo felicidades por instalar libreboot, pero la neta no se me ocurre nada así que para despues pe :b

## Coreboot

Coreboot es considerablemente más difícil de instalar, ya que uno de los principales objetivos de libreboot es hacer coreboot más fácil. Uno aquí debe proporcionar sus propios blobs binarios, eso es algo de lo que ya me encargué al momento de portear Libreboot para el Thinkpad X280, pero eso esa historia de [link a mi post del blog donde hablo sobre mi experiencia porteando libreboot al thinkpad x280](https://www.example.com)

### Rutas

Antes de todo, para hacer todo esto se requieren de demasiadas rutas distintas 
por lo que puede llegar a ser confuso si no se tiene demasiada experiencia, así 
que les propongo la siguiente organización donde tenemos un directorio central
donde irán los demás repositorios, esto para tener todo ordenado y puedan 
simplemente copiar y pegar los comandos en caso de que ya lo entiendan bien todo.

```bash
.
├── coreboot
├── deguard
├── Dell-PFS-BIOS-Assembler
└── original_bios
```


Por ahora no es necesario que crees ninguno de los directorios, ya que muchos de
ellos son repositorios que vamos a clonar más tarde, lo que si que necesitas es crear el directorio
$original_bios$.

Empezando con el siguiente árbol de directorios:

```bash
.
└── original_bios
```

Y como ya lo debieron haber adivinado, aquí es donde pondremos nuestro archivo $x280_original_bios_1.bin$ que extrajimos en el paso [hacer el backup del bios][### Hacer el backup ahora 🪑] aquí ya no es opcional hacer el backup del bios orignal porque necesitamos extraer los blobs binarios por nuestra cuenta.
Especificamente los siguientes:

1. ifd.bin (Flash Descriptor)
2. me.bin (Intel Management Engine)
3. gbe.bin (Gigabit Ethernet)

Pero bueno, nom me adelanto, por ahora deberemos quedar con el siguiente árbol de directorios:

```bash
.
└── original_bios
    └── x280_original_bios_1.bin
```

Ya empezando con lo bueno, debemos construir el toolchain de coreboot y como no podría 
ser de otra manera, la [documentación oficial](https://doc.coreboot.org/tutorial/part1.html)  
tiene un tutorial bastante detallado de lo que se necesita

### Compilar toolchain de coreboot

```bash
git clone https://review.coreboot.org/coreboot
cd coreboot
make crossgcc-i386 CPUS=$(nproc)
```

Lo de más no es necesario seguirlo ya no queremos la configuración para una maquina virtual 
con qemu, si no del Thinkpad X280. Tambien a recalcar que este proceso va a tardar bastante, 
por lo que idealmente lo dejarías de fondo mientras que haces otra cosa.

Ahora hay que instalar ifdtool para poder extraer nuestros blobs binarios, para eso se ejecutan 
los siguientes comandos:

### instalar ifdtool

```bash
cd ./util/ifdtool
sudo make install
```
Ya con ifdtool instalado, volvemos al directorio donde tenemos nuestro bios original

```bash
cd ../../../original_bios
ifdtool -x -p sklkbl x280_original_bios_1.bin
```
Acá verás algunos fallos, pero no te preoucpes, no son relevantes, los 2 archivos que necesitamos se extrajeron correctamente.

```bash
cp flashregion_0_flashdescriptor.bin ../coreboot/binaries/ifd.bin
cp flashregion_3_gbe.bin binaries ../coreboot/binaries/gbe.bin
```

En este punto puede que te estés preguntando ¿Y el me.bin? pues ese no es neceario. Ya que 
nosotros vamos a construir uno propio ya con el Intel Management Engine ya neutralizado, 
de no hacerlo nuestra imagen de coreboot no va a arrancar nunca (me pasó)

### Construir me.bin arrancable 

Primero necesitamos estar en el directorio principal y clonar los siguientes repositorios:

```bash
git clone https://github.com/vuquangtrong/Dell-PFS-BIOS-Assembler
git clone "https://review.coreboot.org/deguard"
```

Quedando con el siguiente árbol de directorios:

```
.
├── coreboot
├── deguard
├── Dell-PFS-BIOS-Assembler
└── original_bios

5 directories
```
Tal y como lo menciona el apartado de [Preparar el ME con deguard](https://doc.coreboot.org/mainboard/lenovo/skylake.html#preparing-the-me-with-deguard)
hace falta descargar el archivo $Inspiron...exe$ para ello hacemos lo siguiente:

```bash
cd Dell-PFS-BIOS-Assembler
wget https://web.archive.org/web/20241110222323/https://dl.dell.com/FOLDER04573471M/1/Inspiron_5468_1.3.0.exe
```

y ahora podemos hacer exactamente el mismo comando que menciona la documentación

```bash
python Dell_PFS_Extract.py Inspiron_5468_1.3.0.exe
```

Acá algo que no menciona la documentación es que no se genera ningún archivo llamado
"me_donor.bin"




## Lo arruiné, como vuelvo al bios original.

Acá en inglich sería algo tipo:

I messed up, how I restore the original bios.



