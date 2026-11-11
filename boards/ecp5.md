# ECP5 — open toolchain (Yosys + nextpnr + prjtrellis)

The ECP5 is the bigger sibling of the iCE40 — more logic, DDR, SERDES — still with a fully open
flow. Boards: ULX3S, OrangeCrab, Colorlight 5A-75B (popular for cheap experimentation).

## Toolchain
Same OSS CAD Suite as iCE40; the tools are `nextpnr-ecp5`, `ecppack`, and a board-specific
programmer (`openFPGALoader` works for most ECP5 boards).

## The three commands
```sh
# 1. synthesis
yosys -p "synth_ecp5 -top top -json top.json" top.v

# 2. place & route (pick your device/package; ULX3S 85F shown)
nextpnr-ecp5 --85k --package CABGA381 --json top.json --lpf top.lpf --textcfg top.config

# 3. pack + program
ecppack top.config top.bit
openFPGALoader -b ulx3s top.bit
```

## Constraints (`top.lpf`)
```
LOCATE COMP "clk" SITE "G2";
IOBUF  PORT "clk" IO_TYPE=LVCMOS33;
LOCATE COMP "led" SITE "B2";
IOBUF  PORT "led" IO_TYPE=LVCMOS33;
```
Pin sites differ per board — use the board's LPF from its vendor/community repo as a starting
point rather than guessing.

## Snags
- Wrong `--85k`/`--25k` or package → P&R targets the wrong die; match your exact part.
- `openFPGALoader` board name must match (`-b ulx3s`, `-b orangeCrab`, …).
- Colorlight boards: check the chip revision — pinouts differ between batches.
