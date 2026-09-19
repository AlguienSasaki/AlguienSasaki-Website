---
title : 'Prep time for coreboot and distros.'
date : 2026-04-05T22:57:55-04:00
tags: ["libreboot","coreboot", "heads",'spi', 'flashprog', "raspberry pi", "pico-serprog", "lbmk", 'high']
summary: "Essential preparation steps to install coreboot/libreboot/heads."
---

This is the biggest section of them all since it covers everything needed to install and compile:

 - Coreboot (from scratch.)
 - Libreboot
 - Heads (Linuxboot)


## Hardware

On the hardware side, we're going to need the following:

- The computer where you plan to install Coreboot/Libreboot/Heads (In my case, the Thinkpad X280 and T480, hopefully the 
family grows bigger in the near future)

![Thinkpad X280 & T480, both running libreboot](/imgs/x280libreboot/too_much_aura.jpg)

- Another Linux computer. And yes, it is completely necessary since we need to send commands to the Raspberry Pi Pico from somewhere. Distro doesn't really matter, though a Debian, Fedora, or Arch base is recommended.
In my case I ran tests with the following hardware: (alongside the Thinkpad X280 & T480. When I flashed one, I used the other for the software setup.)

1. Ideapad S145-14AST with an AMD A9-9425 (and 4GB of RAM @ 2400MHz) running Debian 13 XFCE
![2026-02-18-00-14-13-985.jpg](/imgs/x280libreboot/s145st_flashing_1.jpg)

2. A friend's ASUS gaming laptop running Debian 13 KDE.
![2026-03-13-12-31-13-354.jpg](/imgs/x280libreboot/gaming_laptop_flash.jpg)


- Raspberry Pi Pico.
Depending on your Pico board, you might need to solder pins/wires.

I recommend getting the Raspberry Pi Pico H, which already has the header pins soldered on (or any other board with pre-soldered pins.)

![Raspberry pi pico H](https://cdn-shop.adafruit.com/970x728/5525-02.jpg)

- Phillips screwdriver, I don't know the exact size, but if it let's you open the computer, yeah, that one.

You can also do what I did and grab:

- Breadboard
- Dupont jumper wires

If I'm brutally honest, anything that makes solid contact with the bios chip will do the job. You could also use:
- SOIC 8 clip.

![A classic](https://tienda.eurocerrajeria.com/57-large_default/pinza-soic-8-pines-pomona.jpg)


- A SOP8 pogo pin probe.
![SOP8 socket holder](https://neven.static.s6.upgates.com/5/563be91dec01e3.jpg)

- Or, if you're an absolute insane like me, you can solder dupont wires directly to the bios chip. (Which is what I ended up doing on my X280 because I can't stand the SOIC 8 clip.)
![Still prefer this over using the SOIC 8 clip](/imgs/x280libreboot/dupont_cables_soldered_directly_to_bios_chip.jpg)

- Or if you're already desperate enough and need another entry in your top character development moments, you can hold the wires down directly with your hands, masking tape, and pure sheer willpower.

(A friend's nephew's cousin's kid told me it works, and I totally trust that guy.)

![Why do I keep doing cursed stuff](/imgs/x280libreboot/cables_making_contact_with_pure_willpower.jpg)


The point is, like I said, the wires just have to make contact with the right pin. The method doesn't really matter.

### Circuit and wiring.

To wire the Raspberry Pi Pico to the clip, we follow the pinout from the [official Libreboot documentation](https://libreboot.org/docs/install/spi.html#wiring)

![pinout rpipico](https://av.libreboot.org/rpi_pico/pinout_serprog.png)
![pinout winbond 8 pins](https://av.libreboot.org/rpi_pico/soic8_pico_pinouts.jpg)

Personally, I found their diagram a bit confusing to read, so I made this one for you because I love you too much:

![My own diagram.](/imgs/x280libreboot/cableado_en_tinkercad_wokwi.png)

Another thing to note: instead of wiring directly to the bios chip on the board, you can use the clip on an external socket/adapter board, which is what I did for the T480:

![SOIC 8 clip holder](https://www.electrokit.com/resource/u2Bd/u0i/JGvjpUeX0M/quick/3d/14/4e91_41024104.jpg)




## Software setup on the other Linux machine.

In theory we could fetch and build every piece individually and get the same result, but thanks to the Libreboot make 
script (lbmk), all these steps become way simpler. (Even if you don't plan to flash Libreboot, you can just delete
the directory when you're done.)

## Clone the LBMK repo.

```bash
git clone https://codeberg.org/libreboot/lbmk
cd lbmk
```

## Required environment variables.
We also need to define an environment variable for the thread count used during compilation. The [official documentation says:](https://libreboot.org/docs/build/index.html)

```bash
export XBMK_THREADS=2
```

That depends entirely on how many CPU cores/threads your machine actually has. To save yourself headaches and squeeze out all performance while compiling, just run:

```bash
export XBMK_THREADS=$(nproc --all)
```
What the command above does is set **XBMK_THREADS** to the output of **nproc --all**, which simply returns an integer with the total available threads on your machine.

The [Libreboot documentation](https://libreboot.org/docs/build/index.html) mentions you need to run:

```bash
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
```

This can be a hassle if you're using your main development machine where you already have your own git username and email set up.
You can try skipping it. In my tests, whether using the dummy variables or my real git config, the resulting image was identical (verified via sha256sum and diff).
Still, sticking to the official guide is a good practice. Another clean option is building inside a secondary machine, LXC container, Docker container, VM, or a chroot environment (which is what I did). Since that's more advanced, I'll leave that choice up to you (And maybe for another post, who knows.
 
### Installing dependencies.

Obviously, otherwise how are we going to build anything.
Keep in mind there is only out-of-the-box support for these base distros:

1. Debian.
2. Fedora.
3. Arch Linux.

(Along with all their derivatives, of course.)

I know I sound like a broken record by now, but always check the official docs (this guide is meant as a walkthrough). That being said, we have the [following commands](https://libreboot.org/docs/build/#first-install-build-dependencies):

``` bash
#./mk dependencies ubuntu #if you use ubuntu
#./mk dependencies fedora43 #for the newest fedora version at the time of writing
#./mk dependencies arch # for arch and derivatives.
./mk dependencies debian --reinstall
```


### Installing Flashprog

In the best case scenario we could just do:
 
```bash
sudo apt install flashprog
```

Currently the package isn't available in the Debian 13 repositories...

But if your using Arch Linux you can just run:

```bash
sudo pacman -S flashprog
```

and you're good.

Run:
``` bash
flashprog
```

In your terminal, instead of getting a *command not found* error, you'll see something like this:
![flashprog working](/imgs/x280libreboot/not_readble_bios.png)


If your distro doesn't have the package in its repos, follow these steps. Otherwise feel free to skip ahead.

Inside the lbmk directory, run:

``` bash
./mk -b flashprog
cd src/flashprog
sudo make install
cd -
```

### Breakdown of the commands.
Here is a breakdown of what each command does (so you don't think I just hacked your system XD.)

This command invokes the lbmk script to fetch the source code and build flashprog:
``` bash
./mk -b flashprog
```

This drops us into the flashprog source directory:
``` bash
cd src/flashprog
```

Installs it system-wide so we can run **flashprog** from anywhere:

``` bash
sudo make install
```
Finally, we return to the root lbmk directory:

``` bash
cd -
```


## Raspberry Pi Pico setup

Looking through other guides, I noticed people tend to overcomplicate this part. It doesn't need to be that way when we can just head over to:

[Pico serprog source code on GitHub](https://github.com/opensensor/pico-serprog)

![Pico serprog GitHub page screenshot](/imgs/x280libreboot/pico_serprog_github_page.png)


Go to the releases tab and download the file:
**pico_serprog.elf**

![Pico serprog GitHub releases page screenshot](/imgs/x280libreboot/pico_serprog_releases.png)

Now plug your Raspberry Pi Pico into the computer:

![Connecting the Raspberry Pi Pico to the computer](/imgs/x280libreboot/conexion_raspberry_pi_pico_con_computadora.jpg)

Open your file manager of choice (in my case, the GNOME default, Nautilus).



Under devices you'll see it mounted like a USB flash drive.

Just drag and drop **pico_serprog.elf** onto the new storage device. That's it.

As you'll see, the storage device unmounts automatically. That's normal; in fact, that's what's supposed to happen.


![drag pico_serprog.elf](/imgs/x280libreboot/arrastrar_archivo.png)

If we list the devices on our machine:
``` bash
ls /dev/ttyACM*
```

A new device (most likely named `ttyACM0`) should show up there.

If all you want is to install Libreboot/Heads (Linuxboot), this is all the setup you need, since their automated build scripts take care of the rest.

If you plan on contributing to the projects or compiling coreboot from scratch, the steps in the next post will be what you need. (Dropping tomorrow for sure, trust me.)
