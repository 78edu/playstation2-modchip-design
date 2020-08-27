# playstation2-modchip-design
<pre>Details on a modchip design. Visual schematics or hdl can be added later.

Modchip for ps2 is a not an actual integrated circuit device, this is an electronic circuit 
with one or more chip packages.
It can contain main logic device (cpld, fpga or fast microcontroller like sx28 by parallax).

<b>How it works?</b>

1.Listen for specific data on parallel bus linked with rom memory chip.

2.After 'signature' match, disable rom chip output to high-z by Chip Enable (CE) pin
and start send changed data immediately from on-modchip flash memory 
(easiest way - to use parallel memory chip).

3.First custom fw data (next - patches) was developed by modchip engineers as a patch. 
It contains MIPS R5900 Emotion Engine code.
Probably, first attempt for this was implemented on big parallel eeprom or 
flash chips (32 mbits),but i don't know why we dont have custom bios for ps2 
but have the tricky modchips.

The playstation 2 rom firmware (also known as ps2 bios) is a set of bootstrap code, 
libraries and executable elf files for almost any occasion (drivers, user interface, etc).
It can be easily read with parallel programmer from ROM chip and later examined 
with MIPS disassembler.

<b>More details and know-how</b>

1.Built around 8 bit comparator and large enough (for modchip flash) binary counter 
with reset feature.
Takes about 15 XOR gates and about 16 D-triggers plus about 10 logic elements 
for "bitmask" detect (read below).

2.Signature is compared from on-modchip memory chip.
After every byte match, there is an increment in binary counter and 
in on-modchip memory too.
If there is, for example 16 bytes match (10000 in binary), we 
obtain the '1' signal to start data changing routine.
Here is the hack: we can add some commands without too much logic elements. 
We can add a bitmask (actually 1 different bit. 2 different bits for 2 commands, 
but not too much to avoid mistakes) in latest 16th byte and use it to start another routine, 
for write data into on-modchip memory. This can be used for firmware update and saving setting.
In an O2 modchip source code i saw asm commands to put data (commands and flash content) 
on the bus and modchip will read it.
It is done by special playstation 2 elf program.
Without firmware upgrade feature it should fit in 15 more logic elements.
With firmware upgrade feature it should fit in about 20 more logic elements.
With firmware update and save settings feature it require more blocks, but it 
built around the concept, where we form an address from byte commands on the bus.
Example: signature (15 bytes) latest byte - match - then we burst all next data 
until end of the flash. Or we can use 2 bytes first to set address counter compare value 
(compared by comparator from 1. because it is unused in data patch routine) 
and when we get an address, the whole modchip system will reset. 
So, we read to exact address and not until end of the flash.
Whole firmware update can be done easily by switch some control pins 
and keep going with Write Enable WE and address counter, but if we want to set 
an address and length for settings and firmware area, we need to use more complicated logic.
Also we need octal tri-state flip-flop logic in our cpld or 
fpga to route data from comparator to bus output. 

So, with a great optimisation and simplification work it 
should fit in 64 logic cell device with all listed features above.
Device such as epm3064 by altera, which you can buy for a 1-2$.
(Roughly, it takes about 3/4 to almost 4/4 of such device but 
require to use dirt hacks and stricted logic sequence without idiot proof design).

3.Patch data should be a program. There is a some source codes of 
such programs i saw in o2 modchip and sx28 based modchips.
Typical program can be resident, i.e. persist in playstation 2 ram for all time of work.
This program can:
1.Patch requiered syscalls and opcodes.
2.Start system functions for load modules and elfs.
3.Check gamepad buttons for multimode and menu features.
4.Provide a gui for modchip settings with simple gamepad navigation.

<b>Conclusion</b>

Even with simple straight data patch (patching OSDSYS elf file to 
something like naplink or stripped and static linked with USBD.irx LaunchElf or custom app) 
we can obtain universal usb loader which can fit in about 32 logic cells.
The one more goal there - we need to patch ROMDIR section to emulate USBD.irx 
presence for not patched LaunchELF
This can be tested even in pcsx2 emulator. 
Just write your elf content instead of osdsys in hex editor and your program will 
run instead of Sony Computer Entertainment logo.

</pre>
