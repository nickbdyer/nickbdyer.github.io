---
layout: post
apprenticeship: true
title: "Quattordici"
---

Design Decisions

Since yesterday was spent investigating potential answers to my temporal
coupling blog post, today's post will be a shorter one. A couple of things came
up over the weekend that are worth noting though.

The story that I am currently working on is allowing a player to choose whether
he/she plays against another Human player or computer opponent. I have decided
that the cleanest way to do this is to build a PlayerFactory. I have
implemented it below:

```java
public class PlayerFactory {

  public List<Player> create(int option) {
    if (option == 1) {
      return Arrays.asList(new Human(X), new Human(O));
    } else if (option == 2) {
      return Arrays.asList(new Human(X), new DumbComputer(O));
    }
    return new
      ArrayList<>();
  }

}
```

A couple of discoveries arose in the construction of this class:

1. There is a very distinct order to the beginning of the game, one that
   somewhat defines how classes can/should be initialised. Based on my current
   implementation, before the Game can be initialised it needs two Players,
   before two Players can be initialised they each need a Mark assigned to
   them. In order to prompt the user for the game type and ultimately mark
   choice the UserInterface needs to be initialised with and input and output
   stream. Finally, the Game currently needs to be running in order to control
   the execution of the UserInterface. A bit of a chicken and the egg
   situation which I will try to resolve soon.
2. I tried very hard to test behaviour rather than implementation, however,
   I think I have fallen into the same trap as before. I am now testing that
   the PlayerFactory produces an list with a suitable Player instance in each
   spot.
3. In order to do the aforementioned implementation testing, I did get to do
   something new, that was to overwrite equals and hashCode for the Player
   implementations. I had come across the requirement in the Java Koans so used
   that to help me write something. Unfortunately, for whatever reason, my
   implementation did not work, but it did have a fantastically useful error
   message:

> ```bash
Expected :java.util.Arrays$ArrayList<[com.company.Human@f91ffb12, com.company.DumbComputer@6a38e57f]>
Actual   :java.util.Arrays$ArrayList<[com.company.Human@f91ffb12, com.company.DumbComputer@6a38e57f]>
```

I then discovered that IntelliJ can generate an equals and hashCode for you
automatically, so used that and it worked perfectly. I will spend sometime
Monday investigating why that happened.


