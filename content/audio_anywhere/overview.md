---
title: "Audio Anywhere"
date: 2020-08-31T08:16:30+01:00
draft: false
type: muses
layout: single
---

# Overview

The ideal of compile once and run anywhere has been a dream in computer science for as long as it has been an area of research. From the early days of Lisp, through to Java and Python with its import ideal. However, to date these offerings, as amazing as they are, have failed to reach performance close to what system languages C and C++ can achieve. 

Over the last few years a new kid on the block has emerged as an interesting inflection point in the search for a compile once, run anywhere target for compiling system based languages. WebAssembly (or Wasm) is an open standard, originally developed by the four main browser vendors, specifying a portable format for executable programs, including interfaces for facilitating interactions between such programs and their host environment.

The goal of Audio Anywhere is to explore the idea of compile once, run anywhere for audio DSP code. Audio Anywhere combines Faust, for audio DSP code, and HTML5 to enable development of modern audio synthesis and effects tools. The Faust DSP code is complied once into WebAssembly, but unlike early work the resulting audio code is not hosted within a browser, but instead translated on the fly to native code running within a hosting application. This means that, in theory, an Audio Anywhere module can run anywhere, including platforms where browsers are not available. For the initial implementation of Audio Anywhere, UIs can be described and implemented in HTML5, but this is not a requirement, as the control component is independent of the audio component. This enables interfaces to range from conventional plugin GUIs, remote controllers connected via the Internet of Musical Things, to Embedded hardware interfaces.   

# Releases

Coming soon, for now you can build it manually.

# Source Code

There are currently three main repos for this work:

* [AA examples](https://github.com/bgaster/aa_examples)
* [AA standalone app](https://github.com/bgaster/aa_standalone)
* [AA Wasmtime runtime](https://github.com/bgaster/aa_wasmtime)