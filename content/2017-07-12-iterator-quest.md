+++
title = "Learning Rust 4: Iterator Quest"
date = 2017-07-12T22:00:00-04:00

[taxonomies]
tags = ["learning-rust"]
+++

Today I was going to write a proper iterator for my grid.  After rereading the [documentation for iterators][iter-docs], I was feeling pretty confident.  I wrote myself a nice little iterator that wrapped the regular `Vec` iterator.  Worked like a charm.  I think the reason I was struggling so much before is because of the things I was trying to do within the `for` loop, not the loop itself.

Of course once I was ready to use my new iterator, I realized that the queue system I use for grid updates completely circumvents the need to loop over each cell's creature.

On the other hand, I do loop over every cell in the render loop.  Previously, I had the grid generate a vector containing just its color information, then pass that to the rendering code.  I wrote another iterator, this time for the color information, and dropped it in to the rendering code without issue.  Woo!

Doing this pointed out how much time I was wasting redrawing each cell during every render pass.  It's already starting to slow down when there's a lot of action on the screen, so I think it's time to replace `piston` and handle my own graphics.

I had planned on doing this anyway.  I really only used `piston` to get up and running quickly, and I decided against using `conrod` for my GUI for the time being.

Originally, I was going to give `vulkano` a shot because the author is making some really accessible tutorials and my graphics are really simple anyway.  Unfortunately, my old Surface Pro 2 needs beta drivers to run Vulkan, so I would only be able to run it on my desktop.  Maybe in the future!

Instead, I'm going with `gfx-rs`.  Hopefully I can get it to be fast enough that I can put off doing any difficult optimization until much later.

**Update:** I just did my first build with full compiler optimizations and it's blazing fast.  Good to know!

[iter-docs]: https://doc.rust-lang.org/std/iter/