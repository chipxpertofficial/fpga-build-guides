# FPGA Build Guides

Board-by-board **build walkthroughs for getting a design onto an FPGA** — the commands, the
gotchas, and the "why it won't program" fixes, for both the open toolchains and the vendor flows.
Written so a first-timer can go from Verilog to a blinking LED without a week of yak-shaving.

## Guides

| Board / family | Toolchain | Guide |
|----------------|-----------|-------|
| Lattice iCE40 (iCEstick, iCEBreaker, TinyFPGA) | Yosys + nextpnr + icestorm (fully open) | [boards/ice40.md](boards/ice40.md) |
| Lattice ECP5 (ULX3S, OrangeCrab, Colorlight) | Yosys + nextpnr + prjtrellis (open) | [boards/ecp5.md](boards/ecp5.md) |
| Xilinx 7-series (Basys3, Arty, Nexys) | Vivado (vendor) | [boards/xilinx-7series.md](boards/xilinx-7series.md) |

(More boards — Gowin, Efinix, Altera/Intel — added over time.)

## The universal FPGA flow
Every board is the same four steps; only the tool names change:
```
Verilog  → Synthesis → Place & Route → Bitstream → Program
          (logic→LUTs) (fit to fabric) (binary)   (load to board)
```
Plus one board-specific file that trips up everyone: the **constraints file** (pin assignments +
clock), which maps your top-level ports to physical pins. Wrong pins = nothing lights up.

## The "blink an LED" litmus test
Each guide ends with a minimal blinky so you confirm the whole toolchain works before touching
real logic:
```verilog
module top (input clk, output led);
  reg [23:0] cnt;
  always @(posedge clk) cnt <= cnt + 1'b1;
  assign led = cnt[23];   // ~a few Hz at a 12-24 MHz clock
endmodule
```
If that blinks, your synthesis → P&R → bitstream → program chain is good, and every later problem
is in your design, not your setup.

## Companion material
- A design to put on the board → [vlsi-verilog-examples](https://github.com/chipxpertofficial/vlsi-verilog-examples)
- Project ideas that target FPGA → [btech-vlsi-projects](https://github.com/chipxpertofficial/btech-vlsi-projects)
- Hands-on FPGA labs → [ChipXpert](https://chipxpert.in/)

## License
MIT — see [LICENSE](LICENSE). Maintained by the ChipXpert team.
