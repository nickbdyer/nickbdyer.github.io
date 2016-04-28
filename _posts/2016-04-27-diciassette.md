---
layout: post
title: "Diciassette"
---

Keeping the Test Suite Fast.

My TicTacToe now has an options for a computer vs computer player. One of the
requests from my mentors was that it be a worthwhile choice. I.e. it doesn't
just rattle through the gameplay on the command line so that all you end up
seeing is the final board state. 

In order to make the game "watchable" I added a delay to the computer players
move algorithm. However, in doing so, it also added the delay to the test
suite. And since the test suite forces a computer player to play through the
entire game loop, it increases the test duration by approximately 1000%.

There are I think there are a number of options here to solve the problem,
either I pass a flag into the method itself to determine if the delay is used
or not, or I use an external environment variable to essentially do the same
thing, but a little bit cleaner. Alternatively without flags I can pass in the
delay in milliseconds. I think this is a nice option, and having checked with
Christoph there is a way to go one step further, a new Class!

##### Current Situation

```java
private int generateMove(List<Integer> availableMoves) {
    Collections.shuffle(availableMoves);
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    return availableMoves.get(0);
}
```

##### New Delayed Computer Player

```java
public class DelayedComputer extends DumbComputer {

    private int delay;

    public DelayedComputer(Mark mark, int delay) {
        super(mark);
        this.delay = delay;
    }

    @Override
    public int choosePosition(UserInterface ui, Board board) {
        try {
            Thread.sleep(delay);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        return super.choosePosition(ui, board);
    }
}

```

I can now test the functionality of this delayed computer player easily, and it
doesn't affect the DumbComputer class at all. Feasibly this could be extended
to add a delay to the PerfectComputer also, but I will save that until it is
implemented fully.


