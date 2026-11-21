# Voodoo FPGA

A SystemVerilog reimplementation of the 3dfx Voodoo Graphics 3D accelerator for FPGA.

This project implements the complete Voodoo 1 3D graphics pipeline in synthesizable RTL. The implementation is based on analysis of the original hardware specifications and the [PCem](https://github.com/sarah-walker-pcem/pcem) emulator's Voodoo emulation code.

To the best of my knowledge, Voodoo FPGA is the first publicly demonstrated 3dfx Voodoo 1 reimplementation to render real 3D applications to a display from a real FPGA (April 26, 2026). It is also the first Voodoo on a Lattice ECP5 / ULX3S, built entirely with open-source FPGA tools, and the first shown running its rendering pipeline at 100&nbsp;MHz on any FPGA (August 2026), 130&nbsp;MHz (October 2026), and now with 160&nbsp;MHz SDRAM and hardware bilinear filtering (November 2026).

## Voodoo on ULX3S FPGA

The design running on real hardware: a [Radiona ULX3S](https://radiona.org/ulx3s/) board with a Lattice ECP5 FPGA, driving an HDMI monitor.

The Voodoo core was re-architected to maximize throughput under the constraints of the board's single 16-bit SDRAM bus. The memory subsystem drives the SDRAM interface at **160&nbsp;MHz** using the FPGA's 2:1 I/O gearing with an **80&nbsp;MHz** core clock, doubling command issue capacity to overlap row commands with data transfers. Framebuffer, depth, and display clients automatically merge consecutive accesses into 32-bit bursts to maximize memory efficiency.

To measure the genuine speed of the FPGA Voodoo core itself, pre-recorded Glide command streams from real games are replayed directly from the board's SD card, bypassing the host PC entirely (see [FPS in Real Games](#fps-in-real-games)). The traces are stored in a compact run-encoded, LZ4-style compressed format and streamed over a dedicated 64&nbsp;MHz SPI link through an on-chip FIFO, which removes most of the host-link overhead. An on-screen overlay shows the current frame rate together with per-game min/avg/max, and an optional real-time performance panel breaks down where the cycles go.

With the 160&nbsp;MHz SDRAM interface and the on-chip texture cache, memory bandwidth is no longer the primary bottleneck. Many games now render at playable frame rates, but it is not possible to play games in real time on the ULX3S because the board lacks a fast host link like PCIe.

Long-term, the goal is to retarget to a PCIe + DDR FPGA card. The likely target is the [ALINX AX7A200B](https://www.en.alinx.com/Product/FPGA-Development-Boards/Artix-7/AX7A200B.html) board — an AMD/Xilinx Artix-7 XC7A200T with 1 GB of 32-bit DDR3 and a PCIe Gen2 x2 edge connector, so the card can plug into a modern Windows 10/11 PC, with a custom kernel driver exposing the card to the stock Glide 2.x DLL.

| | |
|:---:|:---:|
| <img src="screenshots/ulx3s.jpg" width="320"> | <a href="https://www.youtube.com/watch?v=m5eE3-TmP6Q"><img src="https://img.youtube.com/vi/m5eE3-TmP6Q/0.jpg" width="320"></a> |
| <a href="https://www.youtube.com/watch?v=ZE4rPW7IYaE"><img src="https://img.youtube.com/vi/ZE4rPW7IYaE/0.jpg" width="320"></a> | <a href="https://www.youtube.com/watch?v=H-VcHg60m8U"><img src="https://img.youtube.com/vi/H-VcHg60m8U/0.jpg" width="320"></a> |
| <a href="https://www.youtube.com/watch?v=kYiTO4DXEKA"><img src="https://img.youtube.com/vi/kYiTO4DXEKA/0.jpg" width="320"></a> | <a href="https://www.youtube.com/watch?v=KTbq-G3HOOc"><img src="https://img.youtube.com/vi/KTbq-G3HOOc/0.jpg" width="320"></a> |
| <a href="https://www.youtube.com/watch?v=_Ttfazh5eC8"><img src="https://img.youtube.com/vi/_Ttfazh5eC8/0.jpg" width="320"></a> | <a href="https://www.youtube.com/watch?v=UybjJaZkl9c"><img src="https://img.youtube.com/vi/UybjJaZkl9c/0.jpg" width="320"></a> |

## FPS in Real Games

Frame rates measured on the ULX3S with hardware bilinear texture filtering and the texture cache enabled with vsync off, replaying each game's Glide command stream from the SD card.

| Game / scene | FPS avg | FPS max |
|---|--:|--:|
| Buffer Clear | 355 | 355 |
| Tomb Raider | 43 | 51 |
| Quake | 29 | 46 |
| Quake II | 22 | 31 |
| Unreal | 15 | 22 |
| Unreal Tournament — Tempest | 21 | 26 |
| Screamer 2 | 23 | 26 |
| Racing — 3dfx demo | 31 | 37 |
| Fight — 3dfx demo | 27 | 27 |

## Video (Simulation)

<a href="https://www.youtube.com/watch?v=CwztiOZkOVs"><img src="https://img.youtube.com/vi/CwztiOZkOVs/0.jpg" width="320"></a>
<a href="https://www.youtube.com/watch?v=hXsq-mxo13E"><img src="https://img.youtube.com/vi/hXsq-mxo13E/0.jpg" width="320"></a>
<a href="https://www.youtube.com/watch?v=Vb6TGa_9cuo"><img src="https://img.youtube.com/vi/Vb6TGa_9cuo/0.jpg" width="320"></a>
<a href="https://www.youtube.com/watch?v=hkKlEKxrMDQ"><img src="https://img.youtube.com/vi/hkKlEKxrMDQ/0.jpg" width="320"></a>
<a href="https://www.youtube.com/watch?v=mtOpSOZ3h8I"><img src="https://img.youtube.com/vi/mtOpSOZ3h8I/0.jpg" width="320"></a>
<a href="https://www.youtube.com/watch?v=dOeNav5UjCw"><img src="https://img.youtube.com/vi/dOeNav5UjCw/0.jpg" width="320"></a>

## Screenshots (Simulation)

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
| **Pipeline** | Highly pipelined: triangle setup → rasterizer → TMU (with bilinear filtering and on-chip texture cache) → pixel shader → depth buffer → framebuffer, valid/ready handshaking with inter-stage FIFOs |
| **FPGA target** | ULX3S (Lattice ECP5 LFE5U-85F, 32MB SDRAM) |
| **Display** | 640×480@60Hz HDMI (DVI-D), RGB565 framebuffer, 8-line circular SDRAM prefetch FIFO |
| **Language** | SystemVerilog |
| **Code size** | ~35,000 lines of RTL |
| **Synthesis** | Open-source toolchain (Yosys, yosys-slang, nextpnr-ecp5) |
| **Verification** | Verilator testbenches, Glide trace replay, PCem bridge for real-time verification |
| **Clocks** | 25 MHz VGA/HDMI pixel output (125 MHz TMDS shift); 80 MHz core clock with 160 MHz SDRAM memory interface (2:1 I/O gearing). |
| **Memory subsystem** | Custom SDRAM controller with 2:1 I/O gearing to 160 MHz SDRAM, fully-pipelined per-client arbiter with address-derived bank interleaving and 32-bit burst pairing. Non-blocking on-chip texture cache for bilinear filtering. |
| **Host interface** | Glide trace replay from the onboard SD card |
| **FPGA resources** | 83% LUTs, 61% FFs, 83% BRAM, 66% DSP (ECP5-85F) |

## Simulation

The design is verified using [Verilator](https://github.com/verilator/verilator). A PCem bridge testbench connects the RTL to PCem over shared memory, [replacing](https://github.com/victor-fisyuk/pcem/blob/voodoo-fpga/src/video/vid_voodoo_fpga_bridge.c) its software Voodoo emulation with the RTL design. This allows real Glide 2.x games running inside PCem to drive the hardware simulation and display rendered output via SDL in real time.

## Status

Started in August 2025. First 3D render (teapot) in October 2025. Valley of Ra demo and Unreal Tournament running in February 2026. ECP5-85F synthesis and place-and-route with timing closure at 50/100 MHz in March 2026. First bring-up on the ULX3S board with HDMI output in April 2026. Pipeline and memory subsystem re-architected into a single-clock, cacheless design in May–June 2026, with the clock raised to 100 MHz in August 2026. In October 2026 the SDRAM scheduling was improved for higher frame rates, the SD-card trace replay was sped up, and on-screen FPS statistics, a performance panel, and a separate bilinear-filtering version with a texture cache were added. By the end of October 2026 the render clock was raised to 130 MHz. In November 2026, the memory subsystem was upgraded to run the SDRAM at 160 MHz using 2:1 I/O gearing with an 80 MHz core clock, and hardware bilinear filtering with a dual-port texture cache was made the default.

All code written by [Claude Code](https://claude.ai).

This is not an open source project. The source code is not publicly available, due to legal concerns around reimplementing 3dfx Voodoo hardware. The SDRAM arbiter, SDRAM controller, and display controller do not implement any Voodoo-specific logic, and are planned to be open sourced separately.

## Copyright

Created by [Victor Fisyuk](https://github.com/victor-fisyuk).

3dfx, Voodoo, and Glide are trademarks of 3dfx Interactive, Inc., now owned by Nvidia Corporation. This project is not affiliated with or endorsed by Nvidia. All other trademarks are the property of their respective owners. This is an independent reimplementation for educational and hobbyist purposes.

## My Voodoo Collection

Original 3dfx Voodoo hardware from my personal collection, which inspired this project.

| | |
|:---:|:---:|
| ![Voodoo card](screenshots/voodoo-001.jpg) | ![Voodoo card](screenshots/voodoo-002.jpg) |
| ![Voodoo card](screenshots/voodoo-003.jpg) | ![Voodoo card](screenshots/voodoo-004.jpg) |
| ![Voodoo card](screenshots/voodoo-005.jpg) | ![Voodoo card](screenshots/voodoo-006.jpg) |
| ![Voodoo card](screenshots/voodoo-007.jpg) | ![Voodoo card](screenshots/voodoo-008.jpg) |
| ![Voodoo card](screenshots/voodoo-009.jpg) | ![Voodoo card](screenshots/voodoo-010.jpg) |
| ![Voodoo card](screenshots/voodoo-011.jpg) | ![Voodoo card](screenshots/voodoo-012.jpg) |
| ![Voodoo card](screenshots/voodoo-013.jpg) | ![Voodoo card](screenshots/voodoo-014.jpg) |
| ![Voodoo card](screenshots/voodoo-015.jpg) | ![Voodoo card](screenshots/voodoo-016.jpg) |
| ![Voodoo card](screenshots/voodoo-017.jpg) | |
