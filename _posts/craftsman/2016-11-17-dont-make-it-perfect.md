---
layout: post
title: "Don't make it perfect, make it work and refine"
authors: ["nick-dyer"]
tags: ["Coding", "Craftsmanship", "Consulting"]
---
Have you ever had trouble starting something? I'm not talking about starting
a car in winter, but some work, maybe a new project or a blog post. Beginning
new projects can be difficult. Can you remember thinking, saying, or hearing any
of the following?

> "I'm still planning it."

> "I need to do some more research."

> "My idea isn't polished enough yet."

> "I wasn't sure on the best way to do it."

### The sticking point

These snippets have something in common. At best they suggest indecisiveness,
at worst they can be symptoms of analysis paralysis. This state—of being
unable to progress past the point of thought and analysis—is not uncommon.
A quick Google search of “analysis paralysis” brings back over three million
results.

As a software developer, there are two situations in which this state often
arises: choosing "what" to do and "how" to do it. In a personal project where we
act as both the product owner and developer, we have to address both.
Contractors are often spared having to deal with the "what" since product or
business owners will exist to make that choice. The "how" is ever present, and
is typically where most of a developer's time is spent.

Not long after starting to program, we realise that there's often more than one
way to solve a “how” problem. This means that a programmer will spend some time
considering the various implementation options. There is certainly value to
this thought process—some ideas can often be ruled out quickly with some
simple thought. The difficult part comes when the obvious options have been
ruled out. From this position we want a way to determine which option is the
best one.

### The paradox of choice

Barry Schwartz speaks about the paradox of choice in his 2005 [Ted
talk](https://www.ted.com/talks/barry_schwartz_on_the_paradox_of_choice?language=en).
He identifies that while freedom of choice is a positive opportunity, the
multitude of choices in society has “left us not freer but more paralyzed, not
happier but more dissatisfied.” Why is having more choices a bad thing?

Analysis paralysis is stressful—it brings with it feelings of anxiety and
self-doubt. These feelings, despite having no tangible output, are using
valuable cognitive resources. Driving while using a mobile phone is well
regarded as dangerous, as it reduces our ability to notice hazards on the road. In
the same vein, when we distract ourselves by worrying, we lower our ability to
think critically or
[creatively](https://www.psychologytoday.com/blog/the-athletes-way/201508/why-does-overthinking-sabotage-the-creative-process)
about the problem at hand.

The second key factor is decision fatigue. The more decisions we make, the more
difficult they become. Successful people including Steve Jobs, Carrie Donovan,
and Mark Zuckerberg are well known for wearing [the same clothes
everyday](http://www.independent.co.uk/news/people/why-mark-zuckerberg-wears-the-same-clothes-to-work-everyday-a6834161.html).
When this was queried, Zuckerberg replied:

> "I really want to clear my life to make it so that I have to make as few 
> decisions as possible about anything except how to best serve this
> community."

Given all these negative consequences to over-analysing, what can we do to
prevent it?

### The quick decision

Having reduced an implementation decision into some sensible options, what can
we do to prevent paralysis setting in? One idea is to flip a coin:

> "When faced with two choices, simply toss a coin. It works not because it
> settles the question for you, but because in that brief moment when the coin
> is in the air, you suddenly know what you are hoping for.”
> —[Kevin Purdy](http://lifehacker.com/5471886/making-a-tough-call)

While this quote tends toward less important decisions, I believe the concept
holds true. Sometimes our instincts can become clouded when we spend too long
analysing our decisions. A coin toss can provide some much needed clarity
when the decision isn't obvious. In cases where an underlying instinctive
choice doesn't come forward, follow the coin toss, because there's a good
chance that the outcomes will be equally viable. The additional benefit here is
in reducing the amount of time we spend thinking. In doing so we can maximise
the time we have with more information.

> "But what if I do the wrong thing, or make the wrong decision?"

In software development, unlike lots of other situations, the cost of making the
wrong decision initially is extremely low. The capabilities of current version
control systems mean that undoing the actual implementation is trivial. When
we make the wrong decision we only lose time. We can reduce the lost time by
minimising the time we spend analysing a decision. However, that does not
mean I am advocating rushing into the production of mediocre code every time
you have a difficult decision. Making a decision is the first step, not the
last.

### Then refine

When we over-analyse we have to compute the expected outcomes from each
decision and compare them. Now that we have something implemented, we have
something tangible to assess. This puts us in a much better place to make
decisions. We can now look at the code and say whether or not we like how it
has turned out.

We can also use this opportunity to improve the code. Seeing the implementation
might bring to light opportunities to refactor, improve naming, or make the code
drier.

This idea parallels a number of existing software practices. In [Clean
Code](https://www.amazon.co.uk/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882),
Robert Martin describes a process he called Successive Refinement, wherein one
writes rough code first before making improvements in its quality. This is
reinforced by his [Three Rules of Test Driven
Development](http://butunclebob.com/ArticleS.UncleBob.TheThreeRulesOfTdd),
which help to ensure that you don’t take on big decisions, but make small
incremental steps to introduce tested production code.

Similarly, in following Kent Beck's [Four Rules of Simple
Design](https://www.amazon.com/gp/product/0201616416?ie=UTF8&tag=martinfowlerc-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0201616416),
we ensure that our code passes a test in the simplest way possible before
iterating over the code again.

It is not clear whether these principles were designed to defend against
paralysis, but each of them goes some way to help prevent it.

### The last decision

Over-thinking is detrimental to our productivity. To avoid it, we should try to
reduce the number of choices we have, timebox our decision making, and
ultimately put something on paper in order to build upon it.

Taking the small leap to try the first option produces evidence upon which we
can make better informed decisions. By working in small iterative steps, we
ensure our decisions are cheap and changeable. We are not making a frivolous
decision in order to avoid difficult questions, we are making a quick decision
in order to make a better one.

Making the decision to start solving a problem quickly does not affect the
quality of its outcome. In our iterative process, we control quality by
deciding when to stop.
