# SillyTestRom

"SillyTestRom" is a simple discrete logic mapper for the famicom/clone/nes that I designed for a game I'm working on:

* 512 KiB PRG-FLASH (re-writabble)
* 8KiB CHR-RAM
* Arbitrary Nametable Mirroring

## Register (0x8000-0x9FFF)

There are no Bus Conflicts:

```
4321 BBBB
|    |
|    +--- PRG Bank (32K@$8000)
+-------- Nametable Mirroring

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

## Flash Saves (0xA000-0xFFFF)

This area is available for writing data to the 39sf flash chip. Check the datasheet for info on how this is done.
