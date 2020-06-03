+++
title = "Learning Rust 3: The First Two Weeks"
date =  2017-07-12T19:00:00-04:00

[taxonomies]
tags = ["learning-rust"]
+++

I intended to write about my experience learning Rust from the beginning, but I was too obsessed with writing the code to stop.  I'm going to try to recap the past two weeks below.  It's long, so here are my main takeaways from this process.

1. The [second edition of the book][rust-second-ed] is fantastic.

2. Winning the fight against the borrow checker isn't about nudging things around until your program compiles.  It's about designing with the borrow checker in mind from the very start.  I haven't done much research on the topic, but I'm guessing that people with solid experience using functional languages have a lot less trouble than people who only have experience with object-oriented languages purely because of the differences in the way you design your programs.

3. `For` loops are hard.  I think this might be a strong bouncing-off point for a lot of people who have programming experience.  It feels like the language is taunting you over something that feels like it should be easy because it's so easy in other languages.  Every single one of my extended fights with the borrow checker involved a loop.  I think it's the only familiar face that works a bit differently in Rust.

4. Rust has the most helpful compiler ever.  I love that it tries to point out the problem using text formatting and colors, AND gives you suggestions about how you might fix the problem.

After reading through the [second edition of the book][rust-second-ed], I started porting [My Little Habitat][mlh] over to Rust.  It was in a state where it had its basic functionality but not much else.  For the most part, it was a matter of recreating things that I already knew how to do in the browser over to a new system.

The [very first version][mlh-old] of My Little Habitat was clunky.  It used SVG to render so it was incredibly slow, and all of the code was written in plain Javascript so I could be messy.  When I moved it over to `canvas2d`, I decided to do it in TypeScript instead.  TypeScript is amazing and it had the added benefit of forcing me to structure everything more carefully.  This was really helpful when I moved over to Rust.  Aside from a little bit of inheritance, most of that structure carried over the Rust pretty well.

I chose to do the graphics using `piston` to make things easier at the beginning.  I had planned on doing the GUI with `conrod` so it seemed like the obvious choice.  I'll be switching it all out for `gfx-rs` or `vulkano` at some point in the future.

Getting colored squares on the screen using my Grid struct was actually pretty easy.  I felt confident about my knowledge of the ownership system and everything just worked out.  Then I started trying to change data stored in the grid and my confidence flew away.

Having essentially copied the algorithm from the TypeScript code, the update loop would iterate over the creatures stored a given Grid cell, then call the creature's individual update method.  The update method took a mutable reference to the grid so that it could get any relevant information about its surroundings and set its new state.

This broke in many ways.  Looping over the grid cells borrowed the grid.  This made it difficult to pass the grid as an argument for the creature's method.  Creatures themselves were just a `trait`, so grid cells were trait objects.  Then boxed trait objects.  Then reference counted trait objects.  Then boxed trait objects again.  Having Creatureness be a `trait` seemed like the right choice since they had unique behaviors and properties that would differ between creatures.  After doing some research, I decided that it maybe wasn't the best choice and began to restructure.

Creatures became a `struct` with a function pointer for the behavior and a map for individual unique properties.

```rust
pub enum Property {
    Integer(i64),
    Decimal(f64),
    Text(String),
    Boolean(bool),
}

pub struct Creature {
    pub creature_type: CreatureType,
    pub color: Color,
    pub properties: HashMap<String, Property>,
    action: fn(&Neighbors) -> Vec<Action>,
}
```

Now my grid was just a `Vec<Creature>` rather than `Vec<Box<Creature>>`, and it became significantly easier to work with.  I still had trouble with my loops though, and nested borrows of the same object were the culprit.

It didn't make much logical sense for an individual creature to have the power to change the whole grid.  Since creating new creature types was supposed to be easy, having a fixed set of general grid operations would solve my problem in addition to simplifying creature logic.

```rust
pub enum Action {
    Set(Position, Creature),
    Clear(Position),
    Queue(Position),
    QueueNeighbors,
    Idle,
}
```

Now I could return a list of messages and feed those into the grid way back in the update loop.  This worked perfectly. This also worked around another problem I was having, which was iterating over the grid cells.  Since the grid really just wraps a `Vec<Creature>` with some methods to calculate 2D indices, I tried to build an iterator that essentially wrapped the `Vec` iterator.  This ended up being a bit of a pain, so I am temporarily looping over indices and pulling the cells from the grid data directly to access their contents.

It worked!  I was finally able to compile the program and watch my plants spread around the grid.  They were weird about it though.  They spread to the bottom-right corner of the graph significantly faster than the other directions.

I actually had this same problem with the web version.  If a plant moved down and/or to the right, the loop hit would hit it again as it was evaluated from top-left to bottom-right.  The first version solved this by flagging those cells to be skipped during the same update.  That's obviously a terrible way to handle it.  Instead, I added a turn queue.  I store the size of the queue at the beginning of the update, then I loop through the queue to determine which cells run their behavior.  If a cell knows that it's going to be active during the following cycle, it tells the grid to queue it back up.  Not only does this run the behaviors in a more organic order, but it also saves me from evaluating behaviors on all of the cells that don't do anything.

One caveat is that changing the creature inside a given cell could potentially change the behavior of its neighbor cells on the next cycle.  To correct this, changing a cell's creature will also queue up all of its neighbors.  This gives the correct behavior **most** of the time, but it can also result in large bursts of activity in certain areas where neighbors are shared by two changing cells.  Filtering the queue for duplicates would be way too expensive so I'll have to figure out a way around this.

So that's where I am now.  The program works and it has a few creatures added to it, although they're a bit simpler than the TypeScript version since they don't have a concept of energy.

Next up:
* Implement creature properties starting with energy.
* Write a proper Grid iterator.
* Consider replacing HashMap<String, Property> with HashMap<String, f64> since this will probably cover all properties.
* Look at ways to deal with queue blobs.

[mlh]: http://picklenerd.com/mylittlehab/
[mlh-old]: http://picklenerd.com/mylittlehab-old/
[rust-second-ed]: https://doc.rust-lang.org/book/second-edition/
