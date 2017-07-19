---
layout: post
title:  "Learning Rust 7: Like an Onion"
date:   2017-07-18 22:00:00 -0400
categories: learning-rust
---
I started implementing separate layers for the ground and the creatures that walk on top of the ground.  It was a little too specialized and awkward to work with.

Instead, grid cells now have layers.  Each cell has a `u8` to `Organism` map representing each layer and the organism that occupies it.  The `get_color` method now returns the color of the topmost layer, and the ColorEnumerator automatically works correctly because it was already using get_color.

The additions were easy.  The modifications are a bit trickier.  Anything that accesses a cell now needs to specify a layer, and there's a lot of code that accesses cells.  Most of the ugliness will be abstracted over at the organism level, but rewriting the main loop and the rest of the backend is taking a while.

I haven't learned much about Rust while working on my project over the past couple days.  I'm more familiar with `HashMap` and read up on `BTreeMap`, but I'm mostly just restructuring.  I'm starting to feel pretty nimble with the language now.  I really only bump into two things with any frequency.

The first is just not knowing the interfaces of things in the standard library.  For example, when I'm calling `get` on a HashMap, the key argument is supposed to be a reference.  These sorts of things happen often enough that I can usually fix them right when RLS calls them out.

The second is nested method calls.  I think that's what you'd call them.  Calling a method within another method or a loop.  I still occasionally bump into the borrow checker when I do this.  It's significantly easier to fix these problems now, but it's not second nature yet.

I also started reading the [Rustonomicon][rustonomicon] this week.  I don't think I'll be writing any unsafe code for this project, but there are a lot of interesting details about the language in there.  Plus the intro said that there would be type theory so I really couldn't stop myself.


[rustonomicon][https://doc.rust-lang.org/nomicon/]  
