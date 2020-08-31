---
title: "Projects"
date: 2019-05-10T13:29:41+01:00
draft: false
type: muses
layout: single
---

# Audio Anywhere

<img class="special-img-class" style="width:20%;height:20%" src="/assets/vl1-emulation-screenshot.png" />

Audio Anywhere is a recent project, which aims to explore the idea of compile once, run anywhere for audio DSP code.

More information can be found [here]({{< ref "/audio_anywhere/overview" >}}).

# SVG Interfaces

<img class="special-img-class" style="width:20%;height:20%" src="/assets/control_graphic.svg" />

SVG Interfaces is an on going project exploring the design and implementation of
custom controller interfaces, bridging the issue of user mapping between action
and sound in interactive music systems.

More information can be found [here]({{< ref "/svg_interfaces/overview" >}}).

# Pat - DSL for exploring Polyrhythm and Polymeter

Music provides a means to explore time by sequencing musical events in a
seemingly endless and expressive way. Pat is a project that takes inspiration from [Tidal Cycles](https://tidalcycles.org) and provides a means to explore advanced rhythmic concepts like polyrhythm and polymeter within the context of designing digital musical instruments.
```
[ a   c   e ]   a   a    | : |    a   c   a   c  | : |    g   g   g   g   g   g   g
```
<button type="button" style="margin:auto;">[View Project]({{< ref "/pat/overview" >}})</button>

# Muses Audio

The Muses audio platform supports multiple sequencers, audio synthesis engines,
effect sends, and a 4 track style digital tape recorder. We have developed Muses
as a research platform for studying the design and implementation of Digital
Musical Instruments (DMIs). A recent screen shot is shown below:

<img class="special-img-class" style="width:50%;height:50%" src="/assets/muses-screen-shot.png" />

For the most part it is written in the systems programming language Rust,
supported by DSP code written in the functional programming language Faust. The
architecture is modular in design supporting audio engines, for both synthesis
and effects processing, to be added easily. 

The Muses audio and effect engines are developed in the DSP functional
programming language Faust, which is a small functional DSL capable of
generating C++ and Rust, along with a selection of other backends. As standard
in audio applications a high priority thread is utilized for audio, along with
threads for handling the UI and input via Open Sound Control (OSC) and/or MIDI.
For most cases our focus is currently on use OSC.

The project is still in very active development and we have not yet made the
source public, but all things going well we plan to do this by the end of summer
2019.

# DSP, Faust and Arrows

The application of functional programming to the field of Digital Signal
Processing is an active area of research.

# Numerical Methods for Physical Modelling and GPUs

Direct numerical methods which have long been studied and applied in physics and mathematics are powerful schemes used to simulate physical phenomena. By simulating the physics of sound wave propagation through instruments and environments, new synthesizers and effects can be accuratly modelled. Physical modelling was for a long time impracticle to compute for most applications. With the level of computational power available today, they are becoming more viable. This project has explored the acceleration of numerical methods for physical modelling by processing on Graphics Processing Units(GPUs).

<img class="special-img-class" style="width:100%;height:50%" src="/assets/plugin_interface.png" />

<button type="button" style="margin:auto;">[View Project]({{< ref "/PM_GPUs/overview" >}})</button>

# Benchmarking

For justification purposes, performance profiling and benchmarking suites have been developed around the design and research of accelerating DSP processes like audio synthesis. Full databases of results and discussions are collected in this project.

<button type="button" style="margin:auto;">[View Project]({{< ref "/benchmarking/overview" >}})</button>