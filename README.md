# Voodoo FPGA

A SystemVerilog reimplementation of the 3dfx Voodoo Graphics GPU for FPGA.

This project implements the complete Voodoo 1 3D graphics pipeline in synthesizable RTL. The implementation is based on analysis of the original hardware specifications and the [PCem](https://github.com/sarah-walker-pcem/pcem) emulator's Voodoo emulation code.

## Video

<a href="https://www.youtube.com/watch?v=CwztiOZkOVs"><img src="https://img.youtube.com/vi/CwztiOZkOVs/0.jpg" width="320"></a>
<a href="https://www.youtube.com/watch?v=hXsq-mxo13E"><img src="https://img.youtube.com/vi/hXsq-mxo13E/0.jpg" width="320"></a>
<a href="https://www.youtube.com/watch?v=Vb6TGa_9cuo"><img src="https://img.youtube.com/vi/Vb6TGa_9cuo/0.jpg" width="320"></a>
<a href="https://www.youtube.com/watch?v=hkKlEKxrMDQ"><img src="https://img.youtube.com/vi/hkKlEKxrMDQ/0.jpg" width="320"></a>
<a href="https://www.youtube.com/watch?v=mtOpSOZ3h8I"><img src="https://img.youtube.com/vi/mtOpSOZ3h8I/0.jpg" width="320"></a>

## Screenshots

### Games

| | |
|:---:|:---:|
| ![Unreal Tournament](screenshots/unreal_tournament_1.png) | ![Unreal Tournament](screenshots/unreal_tournament_4.png) |
| ![Unreal Tournament](screenshots/unreal_tournament_2.png) | ![Unreal Tournament](screenshots/unreal_tournament_3.png) |
| ![Need for Speed II](screenshots/need_for_speed_II.png) | ![Quake 2](screenshots/quake2.png) |
| ![3DMark99](screenshots/3dmark99_1.png) | ![3DMark99](screenshots/3dmark99_2.png) |
| ![Unreal](screenshots/unreal.png) | |

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
| **Pipeline** | Highly pipelined: rasterizer → TMU (32-stage) → pixel shader (10-stage) → depth buffer (3-stage) → framebuffer (3-stage), valid/ready handshaking with inter-stage FIFOs |
| **FPGA target** | ULX3S (Lattice ECP5 LFE5U-85F, 32MB SDRAM) |
| **Display** | VGA display controller with SDRAM prefetch buffer |
| **Language** | SystemVerilog |
| **Code size** | ~18,000 lines, 20+ RTL modules |
| **Synthesis** | Open-source toolchain (Yosys, yosys-slang, nextpnr-ecp5) |
| **Verification** | Verilator testbenches, Glide trace replay, PCem bridge for real-time verification |
| **Clocks** | 25.175 MHz VGA output, 50 MHz render pipeline, 100 MHz SDRAM subsystem with per-client CDC bridges. Timing closure achieved through deep pipelining, basic floorplanning, etc. |
| **Memory subsystem** | Custom SDRAM controller, 7-client arbiter with bank interleaving, 4-level cache hierarchy (texture, depth, FB write-combining, FB read) |
| **FPGA resources** | 68% LUTs, 38% FFs, 37% BRAM, 70% DSP (ECP5-85F) |

## Simulation

The design is verified using [Verilator](https://github.com/verilator/verilator). A PCem bridge testbench connects the RTL to PCem over shared memory, [replacing](https://github.com/victor-fisyuk/pcem/blob/voodoo-fpga/src/video/vid_voodoo_fpga_bridge.c) its software Voodoo emulation with the RTL design. This allows real Glide 2.x games running inside PCem to drive the hardware simulation and display rendered output via SDL in real time.

## Status

Started in August 2025. First 3D render (teapot) in October 2025. Valley of Ra demo and Unreal Tournament running in February 2026. ECP5-85F synthesis and place-and-route with timing closure at 50/100 MHz in March 2026.

Work in progress. The 3D rendering pipeline is functional and runs Glide 2.x games correctly in simulation. FPGA synthesis and place-and-route are complete, hardware testing is the next step.

All code written by [Claude Code](https://claude.ai).

This is not an open source project. The source code is not publicly available.

## Copyright

3dfx, Voodoo, and Glide are trademarks of 3dfx Interactive, Inc., now owned by Nvidia Corporation. This project is not affiliated with or endorsed by Nvidia. All other trademarks are the property of their respective owners. This is an independent reimplementation for educational and hobbyist purposes.
