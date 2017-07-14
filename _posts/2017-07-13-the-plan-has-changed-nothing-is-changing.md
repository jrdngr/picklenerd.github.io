---
layout: post
title:  "Learning Rust 5: The Plan Has Changed.  Nothing is Changing."
date:   2017-07-13 22:30:00 -0400
categories: learning-rust
---
Last night the slowness of my program got to me.  As part of my attempt to speed up rendering, I decided that I'd switch out `piston` for `gfx-rs` early and just do everything directly.  This would probably be a bit slower in the near-term since I don't know what I'm doing, but it would probably be faster in the future since I wouldn't have any overhead from stuff in `piston` that I'm not taking advantage of.

Then I remembered that I can change the optimization level of the compiler.  Since I hadn't done it yet, I maxed it out and ran the program.  It's incredibly fast.  So fast that I'll have to slow everything down to make it look pretty again.  This shouldn't really be a surprise considering how simple this program is at the moment.  I was actually starting to feel disappointed that Rust wasn't helping my performace at all.  Now I'm absolutely thrilled that I managed to get things running in Rust.  This is going to be so awesome.

The other problem I ran into was some obnoxiously complicated code when I tried to add properties to my creatures.  Since creatures are implemented as a struct with a function pointer to their behavior, the behavior isn't a method.  Now I have to pass into the function any information about that creature that I want to use, even though conceptually this action is running on the creature.  *Now that I've typed this out, I can obviously just pass the creature struct instance.  Duh.*

Adding these extra pieces of information to the function meant that I also had to go in and edit all 6 creature files every single time I changed something.  Now part of this is my fault for getting ahead of myself and writing a bunch of creatures, and I think I can just tell the compiler to ignore them.  On the other hand, adding new creatures is supposed to be relatively easy.  That's a primary design goal that's not changing.

So now I'm reconsidering the structure of the creatures.  I tried trait objects again, but I ran into the same problems trying to fit them into some of my other code, and I'm also not thrilled about this dynamic dispatch business that I've been reading about.

For the time being, I'm going to stick with the struct and try to be a little more clever about how I pass things around.  I also think it's time to ask for advice.  Like from a human.

**Update:**
Given that I already had this act method wrapping the internal function, this was a 5 second fix.  I didn't even get to be clever, but at least I can put off talking to a human for a little while longer.

{% highlight rust %}
{
pub struct Creature {
    pub creature_type: CreatureType,
    pub color: Color,
    pub properties: HashMap<String, Property>,
    action: fn(&Creature, &Neighbors) -> Vec<Action>,
}

impl Creature {
    pub fn act(&self, neighbors: &Neighbors) -> Vec<Action> {
        (self.action)(self, neighbors)
    }
}
{% endhighlight %}
