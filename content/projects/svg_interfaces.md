---
title: "SVG Interfaces"
date: 2019-05-11T08:16:30+01:00
draft: false
---

SVG Interfaces is an on going project exploring the design and implementation of
custom controller interfaces, bridging the issue of user mapping between action
and sound in interactive music systems. We have developed a simple framework
utilizing functional specifications for musical interfaces and their mappings. The system
is based on a subset of Scalable Vector Graphics (SVG); interfaces
are described using a simple Haskell based 'controller DSL' or equally using
a vector drawing application (i.e. Illustrator). 

## An Example

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
```

Rendered as an SVG:

<img class="special-img-class" style="width:50%;height:50%" src="/assets/lightpad.svg" />