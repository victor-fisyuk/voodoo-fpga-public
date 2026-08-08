# Voodoo FPGA

A SystemVerilog reimplementation of the 3dfx Voodoo Graphics 3D accelerator for FPGA.

This project implements the complete Voodoo 1 3D graphics pipeline in synthesizable RTL. The implementation is based on analysis of the original hardware specifications and the [PCem](https://github.com/sarah-walker-pcem/pcem) emulator's Voodoo emulation code.

## Voodoo on ULX3S FPGA

The design running on real hardware: a [Radiona ULX3S](https://radiona.org/ulx3s/) board with a Lattice ECP5 FPGA, driving an HDMI monitor.

The Voodoo core was re-architected to run faster and to follow the original Voodoo 1 hardware more closely. The rendering pipeline and the SDRAM subsystem, which previously ran on separate clocks (a 50 MHz pipeline and a 100 MHz memory subsystem), now share a single clock domain that has been progressively raised — currently **100&nbsp;MHz** — through deeper pipelining, registered-DSP retiming, and floorplanning. The intermediate rendering caches (texture, depth, and framebuffer) were removed entirely — every pipeline stage now reads and writes SDRAM directly through a fully-pipelined, per-client arbiter. Previously a cache miss stalled the whole pipeline until the data arrived; now each stage issues its memory accesses without blocking and hides the SDRAM latency, so a slow memory access no longer stalls the work behind it.

To measure the genuine speed of the FPGA Voodoo core itself, a pre-recorded Tomb Raider Glide command stream was replayed directly from an SD card, bypassing the host link entirely. In this mode the game runs at about 8-15 FPS in gameplay on the ULX3S — the actual throughput of the Voodoo pipeline on this board — and 30 FPS on the much simpler title screen.

With a host PC driving the FPGA over the 2 Mbaud UART debug link, the observed frame rate drops below 1 FPS: the UART is the dominant bottleneck in that setup.

The real bottleneck is the board's single 16-bit SDR SDRAM chip, shared between framebuffer reads and writes, depth, texture, and the display controller. Long-term, the goal is to retarget to a PCIe + DDR FPGA card; PCIe and DDR lift both ceilings and put playable frame rates within reach.

| | |
|:---:|:---:|
| <img src="screenshots/ulx3s.jpg" width="320"> | <a href="https://www.youtube.com/watch?v=kYiTO4DXEKA"><img src="https://img.youtube.com/vi/kYiTO4DXEKA/0.jpg" width="320"></a> |
| <a href="https://www.youtube.com/watch?v=KTbq-G3HOOc"><img src="https://img.youtube.com/vi/KTbq-G3HOOc/0.jpg" width="320"></a> | <a href="https://www.youtube.com/watch?v=_Ttfazh5eC8"><img src="https://img.youtube.com/vi/_Ttfazh5eC8/0.jpg" width="320"></a> |
| <a href="https://www.youtube.com/watch?v=UybjJaZkl9c"><img src="https://img.youtube.com/vi/UybjJaZkl9c/0.jpg" width="320"></a> | |

## Video

<a href="https://www.youtube.com/watch?v=CwztiOZkOVs"><img src="https://img.youtube.com/vi/CwztiOZkOVs/0.jpg" width="320"></a>
<a href="https://www.youtube.com/watch?v=hXsq-mxo13E"><img src="https://img.youtube.com/vi/hXsq-mxo13E/0.jpg" width="320"></a>
<a href="https://www.youtube.com/watch?v=Vb6TGa_9cuo"><img src="https://img.youtube.com/vi/Vb6TGa_9cuo/0.jpg" width="320"></a>
<a href="https://www.youtube.com/watch?v=hkKlEKxrMDQ"><img src="https://img.youtube.com/vi/hkKlEKxrMDQ/0.jpg" width="320"></a>
<a href="https://www.youtube.com/watch?v=mtOpSOZ3h8I"><img src="https://img.youtube.com/vi/mtOpSOZ3h8I/0.jpg" width="320"></a>
<a href="https://www.youtube.com/watch?v=dOeNav5UjCw"><img src="https://img.youtube.com/vi/dOeNav5UjCw/0.jpg" width="320"></a>

## Screenshots

### Games

| | |
|:---:|:---:|
| ![Unreal Tournament](screenshots/unreal_tournament_1.png) | ![Unreal Tournament](screenshots/unreal_tournament_4.png) |
| ![Unreal Tournament](screenshots/unreal_tournament_2.png) | ![Unreal Tournament](screenshots/unreal_tournament_3.png) |
| ![Need for Speed II](screenshots/need_for_speed_II.png) | ![Quake 2](screenshots/quake2.png) |
| ![3DMark99](screenshots/3dmark99_1.png) | ![3DMark99](screenshots/3dmark99_2.png) |
| ![Unreal](screenshots/unreal.png) | ![Tomb Raider](screenshots/tomb_raider.png) |

### 3dfx Demo Programs

| | |
|:---:|:---:|
| ![Valley of Ra Demo](screenshots/fight.png) | ![Racing Demo](screenshots/racing.png) |
| ![Anubis](screenshots/anubis.png) | ![Beethoven Bust](screenshots/ludwig.png) |
| ![Martini Glass](screenshots/martini.png) | ![Dragon](screenshots/dragon.png) |
| ![VW](screenshots/vw.png) | ![Teapot](screenshots/teapot.png) |
| ![Textured Cube](screenshots/texcube.png) | ![Gouraud Cube](screenshots/cube.png) |

### Glide SDK Test Programs

| | |
|:---:|:---:|
| ![3dfx Splash Screen](screenshots/splash_screen_2.png) | ![3dfx Splash Screen](screenshots/splash_screen_1.png) |
| ![Texture Lighting](screenshots/texture_lighting_modes_1.png) | ![Texture Lighting](screenshots/texture_lighting_modes_2.png) |
| ![Texture Lighting](screenshots/texture_lighting_modes_3.png) | ![Texture Lighting](screenshots/texture_lighting_modes_4.png) |
| ![Texture Lighting](screenshots/texture_lighting_modes_5.png) | ![Texture Alpha](screenshots/texture_alpha.png) |
| ![Texture Compositing](screenshots/texture_compositing_1.png) | ![Texture Compositing](screenshots/texture_compositing_2.png) |
| ![Texture Compositing](screenshots/texture_compositing_3.png) | ![Fog & Multi-pass Texturing](screenshots/fog_with_multi_pass_texturing.png) |
| ![Depth Bias](screenshots/depth_bias.png) | ![Iterated Alpha](screenshots/iterated_alpha.png) |
| ![Gouraud Triangle](screenshots/gouraud_triangle.png) | ![Line Drawing](screenshots/draw_lines.png) |

## Technical Overview

| | |
|---|---|
| **Voodoo variant** | Voodoo 1 (SST-1) only |
| **Pipeline** | Highly pipelined: triangle setup → rasterizer → TMU → pixel shader → depth buffer → framebuffer, valid/ready handshaking with inter-stage FIFOs |
| **FPGA target** | ULX3S (Lattice ECP5 LFE5U-85F, 32MB SDRAM) |
| **Display** | 640×480@60Hz HDMI (DVI-D), RGB565 framebuffer, 8-line circular SDRAM prefetch FIFO |
| **Language** | SystemVerilog |
| **Code size** | ~30,000 lines of RTL |
| **Synthesis** | Open-source toolchain (Yosys, yosys-slang, nextpnr-ecp5) |
| **Verification** | Verilator testbenches, Glide trace replay, PCem bridge for real-time verification |
| **Clocks** | 25 MHz VGA/HDMI pixel output (125 MHz TMDS shift); single 100 MHz render + SDRAM clock domain. Timing closure achieved through deep pipelining, registered-DSP retiming, and floorplanning. |
| **Memory subsystem** | Custom SDRAM controller on the shared render clock (CL=3), 7-client fully-pipelined arbiter with per-client request FIFOs and address-derived bank interleaving to overlap command overhead with data. Each pipeline stage accesses SDRAM directly — no rendering caches — matching the original Voodoo 1 architecture. |
| **FPGA resources** | 52% LUTs, 43% FFs, 36% BRAM, 58% DSP (ECP5-85F) |

## Simulation

The design is verified using [Verilator](https://github.com/verilator/verilator). A PCem bridge testbench connects the RTL to PCem over shared memory, [replacing](https://github.com/victor-fisyuk/pcem/blob/voodoo-fpga/src/video/vid_voodoo_fpga_bridge.c) its software Voodoo emulation with the RTL design. This allows real Glide 2.x games running inside PCem to drive the hardware simulation and display rendered output via SDL in real time.

## Status

Started in August 2025. First 3D render (teapot) in October 2025. Valley of Ra demo and Unreal Tournament running in February 2026. ECP5-85F synthesis and place-and-route with timing closure at 50/100 MHz in March 2026. First bring-up on the ULX3S board with HDMI output in April 2026. Pipeline and memory subsystem re-architected into a single-clock, cacheless design in June 2026, with the clock raised to 100 MHz in August 2026.

All code written by [Claude Code](https://claude.ai).

This is not an open source project. The source code is not publicly available.

## Copyright

Created by [Victor Fisyuk](https://github.com/victor-fisyuk).

3dfx, Voodoo, and Glide are trademarks of 3dfx Interactive, Inc., now owned by Nvidia Corporation. This project is not affiliated with or endorsed by Nvidia. All other trademarks are the property of their respective owners. This is an independent reimplementation for educational and hobbyist purposes.
