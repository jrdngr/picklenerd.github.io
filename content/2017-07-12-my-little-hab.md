---
layout: post
title:  "Learning Rust 2: The Project"
date:   2017-07-12 08:25:00 -0400
categories: learning-rust
---
The project I'm building in Rust is something I've been wanting to make for a long time.  It's a graphical grid-based life simulator.  You can play with the most recent web version [here][mlh].  Pick a creature on the right and draw with it in the box on the left.

I started with a web version so that it could be widely accessible and people definitely seem to enjoy the early versions, but I'm already running into performance issues and there are a lot of features that I'd like to implement that will bog it down even more, which is why I'm focusing on the [Rust version][mlh-github] first.  I'd like to port what I can back to the web eventually.

Some of the features that will eventually be added are:

* Variable properties that are used in creature behaviors.  Some of these will have associated energy/unit costs and will vary slightly between generations so that creatures can evolve.
* The ability to inspect a single creature to see its properties and other stats.
* Customizable creatures along with a small scripting language to define their behaviors.
* A ground layer that follows the same rules.  This would allow dynamic environments to form and could shape creature behavior in interesting ways.
* Speed controls, possibly including the ability to reverse time.

Some of these features will also migrate to the Typescript version in the future, and customized creatures will be determined by a common text format that will be portable between the two systems.

The overall goal here is to build a system that lets me build systems.  I like watching complex group behavior develop out of simple individual behaviors.  My favorite example is the Parasite creature in the [web version][mlh].  Their logic is similar to Plant logic with a few small changes.  Plants will simply spread into nearby empty spaces if there are any available.  Parasites will spread into nearby Plant spaces if they're available.  The only differences are that Parasites spread a bit more slowly than plants, and Parasites have neighborhood population limits.  If a Parasite has more than 4 Parasite neighbors, they'll start to die.  

Since Parasites leave behind empty space as they die from overpopulation and since Parasites can't spread into empty space, they end up spreading out into a ring.  These rings will occasionally have thin enough walls that a plant can slip inside.  Since the Plant spreads faster than the Parasite, it starts growing inside the ring.  This gives the inner Parasites something to eat, so they start following the Plants.  This turns into a neat animation in the middle of the ring.  I like to think of it as a farm that the Parasites are building to feed themselves.

![parasites][img-parasites-1]

This little farm gives Parasites a renewable food source and makes it possible for more Parasites to exist at one time.  They also tend to be isolated in the middle of a bunch of empty space.  Since Herbivores can only eat Plants and die without food, it's very difficult for them to reach these Parasite farms.  So not only are the Parasites growing their own food, but their food source is well protected against rival species.


And that all just happened.  Nothing was designed to work that way.  You can see just how successful this Parasite farming behavior is over long periods of time.  Using the [web version][mlh], put down a Plant in the middle of the grid, then put a Parasite in the middle of the growing Plant blob.  Once the farm starts forming, place an Herbivore (or a bunch) around the outside of the ring.  Then just let it go.  The Herbivores will cause the ring to break, but the farms keep going and the Herbivores can't survive in the middle of the grid, while the Parasites dominate it.  Cool!

![parasites][img-parasites-2]

[mlh]: http://picklenerd.com/mylittlehab/
[mlh-github]: https://github.com/picklenerd/my-little-habitat

[img-parasites-1]: https://picklenerd.github.io/images/mlh1.gif
[img-parasites-2]: https://picklenerd.github.io/images/mlh2.gif
