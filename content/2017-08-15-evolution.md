---
layout: post
title:  "Learning Rust 9: Evolution"
date:   2017-08-15 08:00:00 -0400
categories: learning-rust
---
This project has been progressing slowly for the past few weeks.  I was losing motivations quickly as this is the fourth or fifth time that I've re-written this same system.  As of Sunday, it finally worked again, and I had my plan rewritten.  Layers are good, grid mutation is good, actions are good.

Then I realized that the evolution system that I'm working toward probably won't fit in to this system very well.  I'm making a system where I can design creatures with specific behaviors that are dynamically tweaked as they "evolve" the parameters for those behaviors.  I want a system where creatures design themselves and whose behavior is based entirely on a set of parameters.  That's going to be a lot more challenging and I still like the first system, so it would be nice to have both.

I decided that grid management should be a trait instead of a struct since this overall grid construct is pretty reusable.

{% highlight rust %}
pub trait GridManager: IntoIterator {
    type GridCellType;
    fn get_cell(&self, x: u32, y: u32) -> &Self::GridCellType;
    fn set_cell(&mut self, x: u32, y: u32, new_grid_cell: Self::GridCellType);
    fn get_color(&self) -> [f32; 4];
    fn update(&mut self);
}
{% endhighlight %}

Now all of my grid management code is part of OrganismGridManager which implements this trait.  This is a pretty huge change, but I think it should have worked like this from the start.  Working through the process of rewriting exposed some of the areas where strong coupling between systems made everything a pain to change. 

Once this is done, I can use my grid system for multiple different things.  For instance, I'll be able to make a cellular automata engine fairly easily.

In other news, I think I can stop calling these posts "Learning Rust".  I'll obviously be learning Rust for a long time, but it's more of a trickle now.  I'll probably make it an even 10 posts and do a wrap-up and summary once my current refactoring project is done, after which this will just be a regular development blog along with some tutorials that cover some of my pain points learning the language.
