---
layout: post
title:  "Learning Rust 7: Like an Onion"
date:   2017-07-18 22:00:00 -0400
categories: learning-rust
---
I started implementing separate layers for the ground and the creatures that walk on top of the ground.  It was a little too specialized and awkward to work with.

Instead, grid cells now have layers.  Each cell has a `u8` to `Organism` map representing each layer and the organism that occupies it.  The `get_color` method now returns the color of the topmost layer, and the ColorEnumerator automatically works correctly because it was already using get_color.

The additions were easy.  The modifications are a bit trickier.  Anything that accesses a cell now needs to specify a layer, and there's a lot of code that accesses cells.  Most of the ugliness will be abstracted over at the organism level, but rewriting the main loop and the rest of the backend is taking a while.

Short post today, but I should have some cool results soon!
