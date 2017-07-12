---
layout: post
title:  "Learning Rust: Intro"
date:   2017-07-11 21:56:00 -0400
categories: learning-rust
---
I have this little [pet project][my-little-habitat] that I've been thinking about for years.  I finally started working on it a few months ago, first in plain Javascript, then in Typescript.  Once the basics were in place, it seemed pretty unlikely that a web browser was going to be able to handle everything I wanted to do.

And so I decided that it was finally time to learn Rust.  I read the [old book][rust-first-ed] late last year and I loved the ideas behind the whole language but I got bored working through the learning resources I found online.  After reading about [Increasing Rust's Reach][rust-reach], I got motivated to try again.  This time, I had the ridiculously awesome [new book][rust-second-ed] and a project to work on.  Given the Rust team's efforts to make the language easier to learn, I wanted to document the learning process from the beginning.  It's two weeks past the beginning now so I'll have to catch up a bit.

Now I'm going to add some Jekyll `features` to this post so that I don't have to look them up in the future.

Here's something I did that feels silly and maybe wrong.

{% highlight rust %}
pub enum Property {
    Integer(i64),
    Decimal(f64),
    Text(String),
    Boolean(bool),
}

pub struct Creature {
    //...
    pub properties: HashMap<String, Property>,
    //...
}
{% endhighlight %}

I want my creatures to have arbitrary properties that can be modified over time.  I also want to implement creatures as a struct and not a trait so that I don't have to deal with trait objects.  This was the most obvious solution.  I haven't actually used it in practice yet so maybe it won't work out, but I still love that this is a thing that I can do, even if it's wrong.


[my-little-habitat]: http://picklenerd.com/mylittlehab/
[rust-first-ed]: https://doc.rust-lang.org/book/first-edition/
[rust-second-ed]: https://doc.rust-lang.org/book/second-edition/
[rust-reach]: https://blog.rust-lang.org/2017/06/27/Increasing-Rusts-Reach.html