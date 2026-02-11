# Voodoo FPGA

A SystemVerilog reimplementation of the 3dfx Voodoo Graphics GPU for FPGA.

This project implements the complete Voodoo 1 3D graphics pipeline in synthesizable RTL. The implementation is based on analysis of the original hardware specifications and the [PCem](https://github.com/sarah-walker-pcem/pcem) emulator's Voodoo emulation code.

## Video

<a href="https://www.youtube.com/watch?v=hXsq-mxo13E"><img src="https://img.youtube.com/vi/hXsq-mxo13E/0.jpg" width="320"></a>
<a href="https://www.youtube.com/watch?v=Vb6TGa_9cuo"><img src="https://img.youtube.com/vi/Vb6TGa_9cuo/0.jpg" width="320"></a>
<a href="https://www.youtube.com/watch?v=hkKlEKxrMDQ"><img src="https://img.youtube.com/vi/hkKlEKxrMDQ/0.jpg" width="320"></a>
<a href="https://www.youtube.com/watch?v=mtOpSOZ3h8I"><img src="https://img.youtube.com/vi/mtOpSOZ3h8I/0.jpg" width="320"></a>

## Screenshots

### Games

| | |
|:---:|:---:|
| ![Quake 2](screenshots/quake2.png) | |

### 3dfx Demo Programs

| | |
|:---:|:---:|
| ![Valley of Ra Demo](screenshots/fight.png) | ![Racing Demo](screenshots/racing.png) |
| ![Anubis](screenshots/anubis.png) | ![Beethoven Bust](screenshots/ludwig.png) |
| ![Martini Glass](screenshots/martini.png) | ![Dragon](screenshots/dragon.png) |
| ![VW](screenshots/vw.png) | ![Teapot](screenshots/teapot.png) |

### Glide SDK Test Programs

| | |
|:---:|:---:|
| ![3dfx Splash Screen](screenshots/splash_screen_2.png) | ![3dfx Splash Screen](screenshots/splash_screen_1.png) |
| ![Textured Cube](screenshots/texcube.png) | ![Gouraud Cube](screenshots/cube.png) |
| ![Texture Lighting](screenshots/texture_lighting_modes_1.png) | ![Texture Lighting](screenshots/texture_lighting_modes_2.png) |
| ![Texture Lighting](screenshots/texture_lighting_modes_3.png) | ![Texture Lighting](screenshots/texture_lighting_modes_4.png) |
| ![Texture Lighting](screenshots/texture_lighting_modes_5.png) | ![Texture Alpha](screenshots/texture_alpha.png) |
| ![Texture Compositing](screenshots/texture_compositing_1.png) | ![Texture Compositing](screenshots/texture_compositing_2.png) |
| ![Texture Compositing](screenshots/texture_compositing_3.png) | ![Fog & Multi-pass Texturing](screenshots/fog_with_multi_pass_texturing.png) |
| ![Depth Bias](screenshots/depth_bias.png) | ![Iterated Alpha](screenshots/iterated_alpha.png) |
| ![Gouraud Triangle](screenshots/gouraud_triangle.png) | ![Line Drawing](screenshots/draw_lines.png) |

## Simulation

The design is verified using [Verilator](https://github.com/verilator/verilator). A PCem bridge testbench connects the RTL to PCem over shared memory, replacing its software Voodoo emulation with the RTL design. This allows real Glide 2.x applications running inside PCem to drive the hardware simulation and display rendered output via SDL in real time.

## Status

Work in progress. The 3D rendering pipeline is functional and runs Glide 2.x demo programs correctly in simulation. FPGA synthesis and hardware testing are next steps.

This is not an open source project. The source code is not publicly available.

## Copyright

3dfx, Voodoo, and Glide are trademarks of 3dfx Interactive, Inc., now owned by Nvidia Corporation. This project is not affiliated with or endorsed by Nvidia. All other trademarks are the property of their respective owners. This is an independent reimplementation for educational and hobbyist purposes.
