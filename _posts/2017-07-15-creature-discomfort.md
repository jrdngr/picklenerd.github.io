---
layout: post
title:  "Learning Rust 6: Creature Discomfort"
date:   2017-07-15 15:00:00 -0400
categories: learning-rust
---
I'm finally settling into a creature design.  I've tried several other setups, but I still like having a generic creature struct with a function pointer for behavior.  It fits well with the two roles that a creature has to fill, storing information about its state, and determining what to do next time it gets a turn.  Any properties specific to that creature are stored in its `properties` HashMap.

This is how the plant is currently defined.

{% highlight rust %}
pub fn new() -> Creature {
    let mut properties: HashMap<String, Property> = HashMap::new();
    properties.insert(String::from(ENERGY_PER_TICK), Property::Integer(1));
    properties.insert(String::from(ENERGY_TO_SPLIT), Property::Integer(100));
    
    Creature {
        creature_type: CreatureType::Plant,
        color: [0.0, 0.5, 0.0, 1.0],
        energy: 0,
        sleep: 0,
        properties: properties,
        action: plant_action,
    }
}

fn plant_action(myself: &mut Creature, neighbors: &Neighbors) -> Vec<Action> {

    let (energy_per_tick, energy_to_split) = get_variables(&myself.properties);

    if myself.energy < energy_to_split {
        if random_percentage(5) {
            myself.energy += energy_per_tick * random_int(0, 3);
        }
        return vec![Action::Idle, Action::Queue(neighbors.pos())];
    } else {
        let empty_neighbors = neighbors.of_type(CreatureType::Empty);
        if empty_neighbors.is_empty() {
            return vec![Action::Idle];
        }
        let new_index = random_index(0, empty_neighbors.len());
        let new_pos = empty_neighbors[new_index].1;

        vec![Action::Set(new_pos, get(CreatureType::Plant)), Action::Queue(neighbors.pos())]
    }
}

fn get_variables(properties: &HashMap<String, Property>) -> (i64, i64) {
    let energy_per_tick: i64 = match properties.get(ENERGY_PER_TICK) {
        Some(&Property::Integer(n)) => n,
        _ => 1,
    };
    let energy_to_split = match properties.get(ENERGY_PER_TICK){
        Some(&Property::Integer(n)) => n,
        _ => 100,
    };

    (energy_per_tick, energy_to_split)
}
{% endhighlight %}

`get_variables` is pulled out to keep the action method a bit cleaner.  I think I can come up with a much better way to get these variables.  The main issue is that I don't know which `Property` variation a given property is going to have.  I have two thoughts about this.  One is that I might be able to make a macro that handles this cleanly, but I'm not ready to dive into macros yet.  The other is to make every property an `f64`.  The `Property` enum currently looks like this:

{% highlight rust %}
pub enum Property {
    Integer(i64),
    Decimal(f64),
    Text(String),
    Boolean(bool),
}
{% endhighlight %}

Changing from HashMap<String, Property> to HashMap<String, f64> would make it much easier to deal with property values, and it would pretty much cover every case except for `Text` which I probably don't even need.

In other news, I made my own struct for the turn queue that uses a HashSet to track duplicate entries.  Now it'll ignore any `push` that contains a value that's already in the queue.  Works like magic and everything is still lightning fast. Hooray!

{% highlight rust %}
pub struct TurnQueue {
    queue: VecDeque<usize>,
    element_set: HashSet<usize>,
}
{% endhighlight %}

**Next up:**
Adding a ground layer.  This will be for stuff like plants that creatures can walk on top of without taking their space.  This feature is the main reason I'm so concerned about performance since it's going to double the size of my grid.  It should be cool though.  It'll behave just like the existing layer so I'll basically have two simulations going at once.  One to determine the landscape and one to determine the action that happens on top of that landscape.