# iCE40 — fully open toolchain (Yosys + nextpnr + icestorm)

The iCE40 is the easiest place to learn the *entire* FPGA flow with 100% open tools — no vendor
account, no licence. Boards: iCEstick, iCEBreaker, TinyFPGA BX, Alchitry Cu.

## Install the toolchain
Most Linux distros package these; otherwise use the prebuilt OSS CAD Suite.
```sh
# via OSS CAD Suite (recommended, self-contained)
# download from github.com/YosysHQ/oss-cad-suite-build, then:
source oss-cad-suite/environment
# now yosys, nextpnr-ice40, icepack, iceprog are on PATH
```

## The three commands
```sh
# 1. synthesis: Verilog -> JSON netlist
yosys -p "synth_ice40 -top top -json top.json" top.v

# 2. place & route: netlist + constraints -> ASCII bitstream
nextpnr-ice40 --hx8k --package ct256 --json top.json --pcf top.pcf --asc top.asc
#   (use --up5k --package sg48 for iCEBreaker/TinyFPGA)

# 3. pack + program
icepack top.asc top.bin
iceprog top.bin           # flashes the board over USB
```

## The constraints file (`top.pcf`) — where everyone gets stuck
Maps top-level ports to physical pins. For the iCEstick:
```
set_io clk  21
set_io led  99
```
Every board has a different pin map — check the board's documentation/pinout. **Wrong pins is the
#1 "it built but nothing happens" cause.**

## Minimal blinky
```verilog
module top (input clk, output led);
  reg [22:0] cnt;
  always @(posedge clk) cnt <= cnt + 1'b1;
  assign led = cnt[22];
endmodule
```
Build the three commands above; the LED should blink at a couple of Hz off the 12 MHz clock.

## Common snags
- `iceprog` permission denied → add a udev rule or run with the right group (not root long-term).
- Board not found → check the USB cable is data-capable and the FTDI driver is present.
- Timing fails → nextpnr prints the max frequency; if your design needs more, that's a real timing
  problem, not a tool issue.
- Wrong `--hx8k`/`--up5k` or package → match your exact chip, or P&R targets the wrong fabric.
