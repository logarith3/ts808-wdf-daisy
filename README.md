# TS808 on Daisy Seed

**A Wave Digital Filter (WDF) model of the Ibanez Tube Screamer 808, ported from a JUCE plugin to the [Daisy Seed](https://electro-smith.com/products/daisy-seed) (ARM Cortex-M7) and optimized to run in real time on embedded hardware.**

> This is a fork of [JamesStubbsEng/TS-808-Ultra](https://github.com/JamesStubbsEng/TS-808-Ultra) (GPL-3.0).
> The circuit model and the desktop plugin are the original author's work.
> **My work in this fork:** the Daisy Seed port, real-time optimization, and measurements.

## Status

| Stage | State |
|---|---|
| JUCE plugin builds and runs | ✅ Done |
| Ported to Daisy Seed, audio passes through | ✅ Done |
| CPU load measurement | ⏳ In progress |
| Optimization (heat / latency) | 🔜 Planned |
| Hardware controls (drive / tone / level pots) | 🔧 Drive pot broken — fixing |
| Documentation & demo | 🔜 Planned |

## Daisy port

### Hardware
- Electro-Smith Daisy Seed (STM32H750, Cortex-M7 @ 480 MHz)
- <!-- TODO: list your pots, jacks, footswitch, board/enclosure -->

### What changed from the desktop plugin
<!-- TODO: what you removed or replaced from JUCE, how the audio callback is structured -->

### Known issues
- The chip runs hot and there is slight latency. Root cause not measured yet — see *Measurements*.
- Drive pot is broken, so drive is currently fixed in code.

## Measurements
<!-- TODO: fill with your own numbers -->

| Block size | Sample rate | CPU load | Notes |
|---|---|---|---|
|  | 48 kHz |  |  |

## Roadmap
- [ ] Measure CPU load per audio block and find the bottleneck (WDF tree / diode solver / oversampling)
- [ ] Optimize: float-only math, cheaper diode solver, smaller oversampling factor
- [ ] Compare desktop vs Daisy output (waveform / spectrum)
- [ ] Fix the drive pot and add hardware controls
- [ ] Rewrite the WDF engine in modern C++ (v2) and compare with v1

## Build
<!-- TODO: how to build and flash (toolchain, make / make program-dfu) -->

## License
GPL-3.0, same as the original project.

---

## Original TS-808-Ultra documentation

*The section below is the upstream project's README, kept for reference.*

Model of the classic Ibanez Tube Screamer 808 using Nodal Analysis and WDF with a few new features added!

Download Windows vst3 and Mac vst3 and AU files here:
https://github.com/JamesStubbsEng/TS-808-Ultra/releases/tag/v1.0.0

![TS-808-Ultra(Screenshot)](https://user-images.githubusercontent.com/64380573/149594162-f6165671-9757-4dc0-9076-938c92f93ed2.PNG)

### Basis of circuits simulated 
The circuit schematics of the TS-808-Ultra are based off Electrosmash's analysis here:
https://www.electrosmash.com/tube-screamer-analysis

#### Clipping Circuit
Jatin Chowdury's WDF library was used to simulate the clipping stage of the TS808.
The library is found here: https://github.com/jatinchowdhury18/WaveDigitalFilters

The clipping stage of the TS808 can be broken down into 3 WDF stages based on ideal opamp circuit analysis.
This is done in the following paper and is shown below:

![WDF_implementation_of_TS808](https://user-images.githubusercontent.com/64380573/149594133-6f77cf1b-f5f6-4c23-8f75-2c05b2dd94b3.PNG)

https://www.researchgate.net/publication/299514713_An_Improved_and_Generalized_Diode_Clipper_Model_for_Wave_Digital_Filters

#### Tone Circuit
The tone circuit analog transfer function can be found by nodal analysis, and is shown in section 4.3 on page 6 of the paper below:
http://dafx.de/paper-archive/2007/Papers/p189.pdf

The analog transfer function can then be converted into a digital transfer function using the bilinear transform (BLT). No frequency warping was used in the plugin, as it was found no frequency warping produces a better sound.

A comparison between the analog and digtial transfer function for the tone circuit is seen below:

![TS808_tone_analog_vs_digit_simulations2](https://user-images.githubusercontent.com/64380573/149831689-ae1cc735-6ed8-411a-a8c6-a9d3b5531bb5.PNG)

### Other features
To make the iconic tube screamer circuit more usable in applications other than in front of a guitar amp the following is added to the plugin: input gain, mix, direct LFP, direct compression, and output gain! These extra features are implemented in the digital domain.

### Pots
#### Drive Pot
The drive pot is modelled with the following function, which emulates a classic audio taper pot:

![audiotaper](https://user-images.githubusercontent.com/64380573/149594797-da323c4f-72a4-48d6-91e9-3cb2d49bbc05.PNG)

#### Tone Pot
According to Electrosmash's analysis, the physical Tube Screamer uses a G taper pot. However, during development, it was found an inverse log taper allows for more control of the upper settings, so the inverse log taper pot emulation below is used in the plugin.

![inverselogtaper](https://user-images.githubusercontent.com/64380573/149595145-69b3351c-b309-4108-aeef-f4625535a0fb.PNG)
