---
layout: post
title: "Diciannove"
---

# Exceptions 

This is difficult post to write because I'm writing this after a long bank
holiday weekend in the UK, and I'm struggling to remember what I was thinking
during my coding Friday morning. My git commit history has fed me some
information, but that captures only what I thought was worth capturing at the
time, it goes very little into the depths of the conversations I had that day!

As I remember it, I was going through the classes in my Java TTT, putting them
in order, tidying up the test suites etc. I came to the classes that implement
my player interface. All players must implement a method called
`choosePosition`.

```java
    int choosePosition(Userinterface ui, Board board)
```

The results of this method are all passed through an input validation check
before they try to mark the board. The edge case that I noticed when looking
through the code was that if any of the computer players were passed a full
board, then an error would occur. Their calculations required a partially full
board, since they resulting return value needs to be a position. 

It should be noted that, a no point will any player be asked to make a move on
a full board, or even a partially full board (with a winner), since the game
loop will break at this point and go to the ending sequence. 

And yet, it seemed entirely possible to me that someone could use the player
classes incorrectly by passing a full board. The decision as to what was the
appropriate action in this case was very conflicted. I spoke to 4 or
5 different craftspeople about this, and each of them had a slightly different
take on it. Their opinions ranged from "Leave it, and ignore that case" to
"Throw an exception and crash everything".

After a number of long conversations, I'm still not sure that I've made the
right decision. I plumped for creating a custom exception, and throwing that
exception within both computer players if they were passed a full board.
I followed advice from our latest Craftswoman, Georgina, on testing for those
exceptions. I have detailed the examples below:

#### DumbComputer Test

```java
@Test
public void throwsExceptionIfBoardIsFull() {
    DumbComputer hal9000 = new DumbComputer(O);
    UserInterfaceSpy ui = new UserInterfaceSpy();
    Board board = new Board();
    makeMultipleMarks(board, 9);

    exception.expect(InvalidMoveException.class);
    hal9000.choosePosition(ui, board);
}
```

#### DumbComputer 

```java
@Override
public int choosePosition(UserInterface ui, Board board) {
    ui.displayComputerThinking();
    if (board.isFull()) {
        throw new InvalidMoveException();
    }
    return generateMove(board.availableMoves());
}
```

#### PerfectComputer Test

```java
@Test
public void throwsExceptionIfBoardIsFull() {
    UserInterfaceSpy ui = new UserInterfaceSpy();
    Board board = new Board();
    setUpBoard(Arrays.asList(O, O, O, O, O, O, O, O, O), board);
    exception.expect(InvalidMoveException.class);
    tron.choosePosition(ui, board);
}
```

#### PerfectComputer 

```java
@Override
public int choosePosition(UserInterface userInterface, Board board) {
    if (board.isFull()) throw new InvalidMoveException();
    if (board.isEmpty()) return chooseRandomCorner();
    resetMoveCalculations();
    makeMoveCalculations(board);
    return bestMove.getKey();
}
```

Some of these methods are due a refactor, try to look past that for the essence
of this post!

The difficulty in this situation is understanding the necessity for a test like
this. Do I need to consider that the public interface to these classes allows
for a full board to be passed to them? Is it realistic to think that another
developer might use this interface incorrectly, given the hypothetical
situation where I am producing this game for someone to use. The reality might
be that I am packaging it for use by others, not passing the source code on for
their use. All the same, is it best practice to keep the code informative for
other developers? It comes down to trade-offs where each option has it's own
pros and cons.


