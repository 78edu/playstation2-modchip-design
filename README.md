# playstation2-modchip-design
<pre>Details on a modchip design. Visual schematics or hdl can be added later.

Modchip for ps2 is a not an actual integrated circuit device, this is an electronic circuit with one or more chip packages.
It can contain main logic device (cpld, fpga or fast microcontroller like sx28 by parallax).

<b>How it works?</b>

1.Listen for specific data on parallel bus linked with rom memory chip.

2.After 'signature' match, disable rom chip output to high-z by Chip Enable (CE) pin
and start send changed data immediately from on-modchip flash memory 
(easiest way - to use parallel memory chip).

3.First changed data (next - patches) was developed by modchip engineers as a patch. 
It contains MIPS R5900 Emotion Engine code.
Probably, first attempt for this was implemented on big parallel eeprom or flash chips (32 mbits), but it was expensive at the moment.
The playstation 2 rom firmware (also known as ps2 bios) is a set of bootstrap code, libraries and executable elf files for almost any occasion (drivers, user interface, etc).
It can be easily read with parallel programmer from ROM chip and later examined with MIPS disassembler.

<b>More details and know-how</b>

1.Built around 8 bit comparator and large enough (for modchip flash) binary counter with reset feature.
Takes about 15 XOR gates and about 16 D-triggers plus about 10 logic elements for "bitmask" detect (read below).

2.Signature is compared from on-modchip
</pre>
