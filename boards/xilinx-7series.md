# Xilinx 7-series — vendor flow (Vivado)

Artix-7 / Spartan-7 boards (Basys3, Arty A7, Nexys A7) use **Vivado** (free WebPACK edition covers
these parts). The concepts are identical to the open flow; the commands differ.

## Two ways to drive Vivado
**GUI:** New Project → add sources + an XDC constraints file → Generate Bitstream → Open Hardware
Manager → Program. Fine for learning.

**Tcl (reproducible, scriptable)** — a minimal non-project flow:
```tcl
read_verilog top.v
read_xdc top.xdc
synth_design -top top -part xc7a35ticsg324-1L   ;# Arty A7-35T
opt_design
place_design
route_design
write_bitstream -force top.bit
```
Run with `vivado -mode batch -source build.tcl`.

## Constraints (`top.xdc`)
```tcl
set_property -dict { PACKAGE_PIN E3 IOSTANDARD LVCMOS33 } [get_ports clk]
create_clock -period 10.000 [get_ports clk]           ;# 100 MHz on Arty
set_property -dict { PACKAGE_PIN H5 IOSTANDARD LVCMOS33 } [get_ports led]
```
Each board ships a master XDC with every pin commented out — uncomment the ones you use. **Wrong
`-part` or pin = build succeeds, board does nothing.**

## Program
Hardware Manager → Open Target → Program Device, or `write_cfgmem` + the board's loader to flash
non-volatile.

## Snags
- Wrong `-part` string → match the exact device/speed/package on your board's silkscreen.
- Clock constraint missing → timing is unconstrained; Vivado warns, and you learn nothing about Fmax.
- Driver/cable issues → install the cable drivers; Basys3/Arty use the onboard FTDI.
