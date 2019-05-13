---
title: "SVG Interfaces"
date: 2019-05-11T08:16:30+01:00
draft: false
---

# Overview

SVG Interfaces is an on going project exploring the design and implementation of
custom controller interfaces, bridging the issue of user mapping between action
and sound in interactive music systems. We have developed a simple framework
utilizing functional specifications for musical interfaces and their mappings.
The system is based on a subset of Scalable Vector Graphics (SVG); interfaces
are described using a simple Haskell based 'controller DSL' or equally using a
vector drawing application (i.e. Illustrator). 

SVG Interfaces a designed as portable format for specifying control interfaces
independenlty of any particular device, however, to show their practical use we
have developed example backends for ROLI's
[Lightpad-M](https://roli.com/products/blocks/lightpad-m) and Sensel's 
[Morph](https://sensel.com/pages/the-sensel-morph).

The details of designing a control interface and supproting tools are detailed
in the [SVG Interface
Documentation](https://github.com/muses-dmi/svg-creator/blob/master/docs/interfaces.md),
and additionaly below is a short introduction to SVG Interface design.  

Interfaces can be described using the Contoller DSL, for example, a common
controller is a **stop** button, which emits a stop message each time it is
pressed:

```haskell
stop ! #x 3 ! #y 3 ! #size 3 
```

Similar to SVG attributes named labels are used to pass information about
particular attributes of a controller, in this case a stop button has an x and y
postion for its top left corner and a size, which sets its width and height in
millimetres.

More generally, a stop button is an instance of a **pad** and could just as easily be
defined as such:
```haskell
pad ! #x 3 ! #y 3 ! #size 3 ! #address "/stop/" 
```

In both cases the following SVG code is produced:
```svg
<rect interface_osc_address="/stop/" 
      width="3" height="3" 
      interface_type="pad"  
      interface_osc_args="" 
      x="3" y="3"/>
```

At this point we have defined only a controller, to create a valid interface, we
must wrap any controllers within an interface itself. It it most simple form
this can be found defining the function ```interface```, so:

```haskell
interface = stop ! #x 3 ! #y 3 ! #size 3 
```

defines the complete interface:

```svg
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN"
    "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
      <svg xmlns="http://www.w3.org/2000/svg" 
        height="15mm" width="15mm"
        viewBox="0 0 15 15"  
        xmlns:xlink="http://www.w3.org/1999/xlink" 
        version="1.11.1" 
        interface_device="lightpad">
        <rect interface_osc_address="/stop/" 
         width="3" height="3" 
         interface_type="pad"  
         interface_osc_args="" 
         x="3" y="3"/>
      </svg>
```

which can now be rendered:

<img class="special-img-class" style="width:25%;height:25%" src="/assets/example1.svg" />

Here the border just highlights the interface's surrounding edge and plays no
part in the final interface. For many interfaces the colour of a controller does
not play a part, hence the default black, but for some, it can be used to
control some aspect of the interface,  e.g. ROLI's Lightpad have RGB LEDS. For
this a standard SVG **fill** attribute can be added:

```haskell
stop ! #x 3 ! #y 3 ! #size 3 # fill "rgb(217,137,188)"
```

and now renderers as:

<img class="special-img-class" style="width:25%;height:25%" src="/assets/example2.svg" />

More complex interfaces are composed from smaller ones using the combinator <>, the following
implements a simple interface with stop, play, and record controls:

```haskell
stop ! #x 27 ! #y 2 ! #size 25
<>
play ! #v1 (22,14) ! #v2 (2,2) ! #v3 (2,27)
<>
record ! #x 69 ! #y 14 ! #r 12
```

And as a rendered SVG:

<img class="special-img-class" style="width:40%;height:40%" src="/assets/example3.svg" />

# A Full Example

A more complete example is shown in the following SVG interface code for a four
button (pad) and four slider controller interface designed for ROLI's
[Lightpad](https://roli.com/products/blocks/lightpad-m):

```haskell
lightpad = 
    mconcat (map p [(0,100), (4, 101), (8, 102), (12, 103)])
    <>
    mconcat (map s [(0,104), (4, 105), (8, 106), (12, 107)])
    where
      p = \(x, cc) -> pad ! #x x ! #y 0 ! #size 3 ! #address "/midicc" 
                          # iargs [cc] # fill "rgb(217,137,188)"
      s = \(x, cc) -> 
        vertSlider ! #x x ! #y 4
                   ! #width 3 ! #height 11 ! #min 0 ! #max 127 ! #address "/midicc" 
                   # fill "rgb(96,95,164)" # iargs [cc]

interface = lightpad
```

This example demostrates the power of a programmatic approach, here combining
well known higher order functions, e.g. map, from function programming to
recursively layout the multiple pads and sliders. 

The above SVG interface specification is complied to an SVG with the [SVG
Creator tool](https://github.com/muses-dmi/svg-creator). The SVG, created with
SVG Creator, is rendered as:

<img class="special-img-class" style="width:50%;height:50%" src="/assets/lightpad.svg" />

# More Information

Tool and documentation for specification of interfaces as SVGs:

   - [SVG Creator tool](https://github.com/muses-dmi/svg-creator).
   - [SVG Interface Documentation](https://github.com/muses-dmi/svg-creator/blob/master/docs/interfaces.md).

Tools for translating SVG Interfaces to the JSON intermidiate representation and different backends:

   - [SVG Interface to IR tool](https://github.com/muses-dmi/svg_interface).
   - [Interface IR to Littlefoot tool](https://github.com/muses-dmi/svg-littlefoot).
   - [SVG Sensel Driver](https://github.com/muses-dmi/sensel_osc).

