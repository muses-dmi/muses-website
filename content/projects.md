---
title: "Projects"
date: 2019-05-10T13:29:41+01:00
draft: false
---

# SVG Interfaces

<img class="special-img-class" style="width:20%;height:20%" src="/assets/control_graphic.svg" />

SVG Interfaces is an on going project exploring the design and implementation of
custom controller interfaces, bridging the issue of user mapping between action
and sound in interactive music systems.

More information can be found [here]({{< ref "/svg_interfaces_src/svg_interfaces" >}}).

# Pat - DSL for exploring Polyrhythm and Polymeter

Music provides a means to explore time by sequencing musical events in a
seemingly endless and expressive way. Pat is a project that takes inspiration from [Tidal Cycles](https://tidalcycles.org) and provides a means to explore advanced rhythmic concepts like polyrhythm and polymeter within the context of designing digital musical instruments.

As a brief example, consider the following pattern and take a listen to what that may sound like below:
```
[ a   c   e ]   a   a    | : |    a   c   a   c  | : |    g   g   g   g   g   g   g
```

More information can be found [here]({{< ref "/not_src/not" >}}).


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

# Physical Modeling with Drums and GPUs 

<img class="special-img-class" style="width:50%;height:50%" src="/assets/plugin_interface.png" />
