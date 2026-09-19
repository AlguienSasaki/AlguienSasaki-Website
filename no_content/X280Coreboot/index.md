---
title : 'Coreboot X280'
date : 2026-04-06T22:57:55-09:00
tags: ["libreboot","coreboot",'thinkpad','x280','spi', 'flashprog', 'moderado']
summary: "Unnecesarily detailed tutorial on how to install libreboot/coreboot in the Thinkpad X280."
---



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




## ifdtool

## Intelmetool

## me_cleaner




