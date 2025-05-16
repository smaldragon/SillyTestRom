# SillyTestRom

"SillyTestRom" is a simple discrete logic mapper for the famicom/clone that I made for my own personal messing around with homebrew, there are currently no plans to use this in anything.

* 512 KiB PRG-FLASH (partially re-writabble for game saves)
* 8KiB CHR-RAM
* Arbitrary Nametable Mirroring!

I mainly see this as an extended AxROM, compared to similar options, the main upsides of this board are low-part count and compatibility with famiclones that don't support 4-screen mirroring.

The board can also be bodged into other configurations. Currently 2 configurations of this board are being considered (by me), A and B.

> **NOTE** Everything here is a WIP and subject to change!

## Hardware

![PCB Render](pcb.png)
![Schematic](schematic.png)

Mapper ICs:
* 74HC377 (8bit register with enable, mapper register)
* 74HC153 (dual 4→1 multiplexer, mirroring logic and chip-enable not gate)
* some diode/resistor logic

## Register (0x8000-0x9FFF)

There are no Bus Conflicts.

Its possible I may switch the PRG Bank control to use A0-3 instead, as code is always aware of which bank its currently running from, this could save code from always having to OR the graphics and bank bits together when only changing graphics values.

### A Wiring

```
Data
MMMM BBBB
|    |
|    +--- PRG Bank (32K@$8000)
+-------- Nametable Mirroring

Potential Address+Data Alternative:

Address | Data
   BBBB | xxxx.MMMM
      |        +-------- Nametable Mirroring
      +----------------- PRG Bank (32K@$8000)
```

Each Mirroring bit controls the nametable (A or B) visible at each of the 4 logical slots, allowing for any type of (non-extended) mirroring, ie:

```
* $Ax -> Vertical
   AB
   AB
* $Cx -> Horizontal
   AA
   BB
* $0x -> Single Screen A
   AA
   AA
* $Fx -> Single Screen B
   BB
   BB
* $6x -> Diagonal
   AB
   BA
 (etc..)
```

## B Wiring

The B version loses the support for horizontal and vertical mirroring, but allows for 4 CHR-RAM banks, this is motivated by the similar cost of 8KiB and 32KiB sram chips.

```
CCMM BBBB
| |  |
| |  +--- PRG Bank (32K@$8000)
| +------ Nametable Mirroring
+-------- CHR Bank (8K@ppu$0000)

Potential Address+Data Alternative:

Address | Data
   BBBB | xxCC.xxMM
      |     |    +------ Nametable Mirroring
      |     +----------- CHR Bank (8K@ppu$0000)
      +----------------- PRG Bank (32K@$8000)
```

In this case the mirroring bits are as follows:

```
(bits)
* 00: AA
      AA

* 01: BA
      AB

* 10: AB
      BA

* 11: BB
      BB
```

## Flash Saves (0xA000-0xFFFF)

This area is available for writing data to the 39sf flash chip. Check the datasheet for info on how this is done.
