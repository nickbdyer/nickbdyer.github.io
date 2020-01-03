---
layout: post
title: "Tickets for Specialised Teams"
---

Agile projects typically use a unit of work known as a ticket, user story, or
increasingly a job story. These tickets are written to specify some new feature
that provides value, to a user of the system. They do not dictate
how the feature should be built, nor do they specify who should build it. As
such, they often assume that the person that is building the feature is capable
of doing anything across the code base, it assumes they are "full-stack".

On a team where some developers identify as “backend”, some as “frontend” and
some as “full-stack”, how can we write tickets that are suitable for everyone?
Can we also esure that tickets are built efficiently?

Let's set the scene, our ticket in question reads like this:

- As a customer
- When I complete a section of the form and click "Next", my progress is saved.
- So that I can finish it later and don't have to complete it in one sitting.

For the sake of this article, assume that we are confident we want to persist
this data to our backend database, not to a browser session.

Our development team is made up of two people, Bertrum Backend and Flavia
Frontend.

Our focus is assuring Bertrum and Flavia can complete this story, while also
ensuring we can deliver on a few extra considerations:

- The story shows what business outcomes and value will be realised after the
  story is finished.
- Re-work is minimised as much as possible.
- Accountability for getting the story over the line is clear.
- Progress on the story during an iteration is transparent.


Possible solutions:

Split the feature into two user stories, one frontend, one backend,  that can
be progressed independently.  Create subtasks for the larger story that can be
progressed independently.  Ask the backend and frontend developers to work
together directly on the story.

## Split the Story:

When splitting a story it's easy to end up in a situation where we lengthen the
total duration of the story, as each side works independently it can take
a long time until we get feedback. We want to prevent long-lived unused code.
It's easy to end up in this situation if for whatever reason one half of the
split feature is blocked. We must be careful that any code being written will
be used very soon by the other person, the distance between delivery of the
backend/frontend stories should be as close as possible to avoid code rot.

Communication is key, without it separate stories can also lead to the
implementation of "what if's", where design discussions can occur for what
"might' be needed by Flavia or Bertrum. The same goes for defensive
programming, where protection is put in place for situations that can never
occur when the front and back are connected. Our aim should be to build "just
enough" to satisfy both sides, thereby delivering the business outcome in the
shortest time.

It is worth pointing out that in some cases it is valid that backend
and frontend stories are split. For example in the case where a backend API
serves a dual purpose for a frontend application and programmatic access via
the API. In this case, the API is in itself it's own product and thus the work
could and probably should be handled with independent outcomes. This, of
course, brings in explicit dependencies on the order in which the build can
happen, which is something we want to avoid where possible due to the slower
feedback loop.

## Create Sub-tasks

This solution is not dissimilar to splitting the stories completely into
backend and frontend. However, the one thing that it maintains is the business
outcomes and value being captured once. It can help to drive away from
speculative features that are unneeded.

Sub-tasks are often useful even without the explicit benefit of managing back
and frontend work. Sometimes stories can be so big they extend over a single
sprint. This situation can still mean progress can be made on sub-tasks, and
importantly that progress is transparent.

One downside to this approach is that it can hide the amount of work that
either side needs to do. Since the work board will likely only show a single
estimation for this split work, it can make accurate capacity planning difficult.

## Work Together

There are at least a couple of ways this could be done, with a few different
outcomes depending on the focus.

We could consider the distinction here between, teams where people are
interested and willing to cross-train. And those that are not. In each case
a slightly different approach is needed, but with a broadly similar process.

We could ask them to work together on designing a contract between the two
pieces of work. In this case, we'd want to pay close attention to any required
changes that we discover and communicate them early. Upfront design of this
contract can only get us so far. The logistical issue here is that one side of
the work could be significantly less than the other, which then leads to one
person starting work on a new feature before this is delivered. Problems can
then occur where discoveries are made on the old story that requires the
"finished" developer to start costly context switching.

Alternatively, it could be that the back and frontend developers pair together
with the explicit intent of cross-training and building towards a "full-stack"
team. This is particularly effective in cases where the business desires
a fully cross-functional team and are in support of this model. Otherwise, it
can produce unwanted reduction in team velocity.

## Final Considerations

We've considered three approaches here, each of which have trade-offs. We have
not discussed the effect any of these decisions would have on the day-to-day
job satisfaction of Bertrum or Flavia. We need to consider that if our
development process doesn't model our team or is resisted by the team, they may
subconsciously try to circumnavigate it.

Unless your project has very explicit back-end and front-end separation, our
favoured approach is for developers to work together and upskill each other. In
our experience, the team of full-stack individuals has time and time again
shown to be the fastest way to build quality software.

