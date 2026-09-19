---
title : 'Desarmar Thinkpad X280'
date : 2026-04-05T22:57:55-05:00
tags: ["dissasembly",'thinkpad','x280', 'facil']
summary: "Tutorial de como desarmar un Thinkpad x280 ft. Locación del chip de BIOS."
---

Este apartado es mas bien inecesario, ya que simplemente debería mandarlos a ver un video de youtube 
(que tampoco considero necesario ya que esta maquina es super fácil de desarmar), pero me sirve para
mostrar la ubicación exacta del chip de bios (y se evitan entrar a un foro ruso como me tocó a mí XD.)

De todos modos, acá un video de desarmado para mis aprendices visuales:

{{< youtube XB3tJnmhmHw >}}

## Remover tapa inferior.

Recordando tener la maquina totalmente apagada.

Ahora debemos quitar los 5 tornillos visibles de la carcasa, la verdad no se que tamaño de destornillador usar, pero el que te perimta quitarlos sirve

![x280bottom.jpg](/imgs/x280libreboot/x280bottom.jpg)
 
## Remover batería principal.

Ya con la maquina abierta es indispensable el retirar cualquier tipo de energía del sistema, tal y como lo indica la [documentación de coreboot](https://doc.coreboot.org/mainboard/lenovo/skylake.html#:~:text=Disconnect/remove%20all%20batteries%20(and%20CMOS%20battery%20if%20equipped).)

Para quitar la bateria es necesario desatornillar los 5 tornillos que la mantienen en su lugar, puedes usar el desarmador que usaste para abrir la tapa del computador.
![x280pcb.jpg](/imgs/x280libreboot/x280pcb.jpg)

![x280battery.jpg](/imgs/x280libreboot/x280battery.jpg)

## Remover CMOS.

{{< alert >}}
**¡Atención!** No te olvides de remover el CMOS solo porque no aparece en el video.
{{< /alert >}}


Para quitar el cmos es necesario quitar la bateria principal, ahora simplemente jalar el conector y listo.

![x280cmos.jpg](/imgs/x280libreboot/x280cmos.jpg)

