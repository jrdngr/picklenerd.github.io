+++
title = "Learning Rust 10: Learned Rust"
date =  2017-08-21
+++

I took a break from this for a few days.  After having the brilliant idea to rip my systems apart after rebuilding them for the 4th time, I had the even more brilliant idea to undo all of that ripping and go back to when it still compiled.  At some point, I'll have to write something new instead of writing the same thing over and over.

So here I am, two months after deciding to tackle Rust and port over my main project.  I'm feeling pretty confident with Rust at this point, and it has basically poisoned every other part of my life.  My Java code at work is changing, I check the subreddit and forums multiple times every day, and I can't even open Unity anymore without feeling like I'm in the wrong place.

I still want to dump Piston, but I think it's the wrong time.  I'd like to start my dive into graphics programming with Vulkan (I know, shut up) but I need a laptop that supports it first and I'm not getting one until I can buy a Surface Book 2.  So maybe never.

Other than that, I'm going to keep chugging along with my plans.  First, I need to tackle the grid manager's usability.  It works ok but my creature code is awkward and annoying to write.  I'm probably going to go back to an enum of standard actions that I'll return in a vector rather than having the creatures directly manipulate the grid.  They'll probably still have access to it in case I want them to do something wacky to each other, but most of their behavior should be simpler to write.

For example, here's my current plant code:

{% highlight rust %}
pub fn plant_action(grid_manager: &mut GridManager, (x, y): (u32, u32)) {
    if utils::random_percentage(95.0) {
         grid_manager.add_to_queue(x, y, LAYER);
    } else {
        let mut new_cell_coordinates = (0, 0);
        let mut set_new_cell = false;
        {
            let neighborhood = grid_manager.get_neighborhood_of_type((x, y), OrganismType::Empty);
            if neighborhood.len() > 0 {
                new_cell_coordinates = utils::random_element(neighborhood.as_slice()).1.borrow().get_position();
                set_new_cell = true;
            }
        }
        
        if set_new_cell {
            grid_manager.set(new_cell_coordinates.0, new_cell_coordinates.1, new_plant());
            grid_manager.add_to_queue(x, y, LAYER);
        }
    }
}
{% endhighlight %}

I have this problem where getting a neighborhood takes an immutable reference to the manager, so I have to drop the reference before I can set the cell.  Then I do some other really awful things to compensate.  I didn't really think about this when I wrote it since I was at peak burnout.  I just wanted it to compile and look pretty when I ran it.

Another thing that bugs me is this fake dynamic dispatch thing that I did.

{% highlight rust %}
pub fn act(grid_manager: &mut GridManager, (x, y, layer): (u32, u32, u32)) {
    let mut cell_type = None;
    if let Some(cell) = grid_manager.get(x, y).borrow_mut().get_layer(layer) {
        cell_type = Some(cell.organism_type);
    }

    if let Some(id) = cell_type {
        match id {
            OrganismType::Plant => plant::plant_action(grid_manager, (x, y)),
            OrganismType::Cow   => cow::cow_action(grid_manager, (x, y)),
            _                   => {},
        }
    }
}
{% endhighlight %}

After reading about static and dynamic dispatch, I ended up with this system rather than trait objects with their own act method because I wanted the performance.  Once I actually thought about it, I decided that this is probably just a slower version of regular dynamic dispatch.  Refactoring it into a trait object is kind of a pain so this is a pretty low priority, but it's still annoying and will be increasingly annoying as I add more creatures.

Once creature writing is cleaned up, I'll write some more creatures that each have a few numerical properties that influence their behavior, then add a rudimentary system of evolution that modifies those properties.  Hopefully, this will produce some interesting results.

Finally, I'll have to step back from coding and do some serious design work.  I need to figure out how the final evoluton system will actually work.  My main task will be to design an energy system that balances utility vs cost.  Every property that a creature has might have an associated energy ratio that alters its energy cost depending on its value.  So a creature might move more quickly, but it's going to lose more energy in the process.  If the property bump is worth the extra energy, it should be more likely to survive.  If I want the idea to be that simple and still produce interesting results, I'm going to have to be clever with the properties.  Or maybe that system won't work at all and I'll have to do something entirely different.

And this concludes my little Learning Rust blog series.  This is obviously just a development blog at this point, as the learning pace has slowed down from "holy shit what is happening!" to "this is a thing that I use."

I'll end with the amazing(ly stupid) lesson I learned tonight.  `unwrap` is a trap!  `if let` your `Option`s.  

Thank you and goodnight!