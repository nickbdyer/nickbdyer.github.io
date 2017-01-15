---
layout: post
apprenticeship: true
title: "Venticinque"
---

# Statelessness

This week, I have been pairing with Mollie on our new internal project, LunchMan&trade;.

In my previous iteration I had planned to get this blog post finished, since my
work with the the Play Framework had pushed me to learn about the benefits of
statelessness in my application. Unfortunately, that iteration was cut short
in order to synchronise with Mollie so we could start this project together.

No matter! As it turns out, the application we have been building together has
already reached a stage where web state has reared its head, and so now I can
talk about it from both perspectives.

From the perspective of TTT, the reason for wanting to engineer statelessness
was to allow more than one person to go to the website and play a game. In
LunchMan the reason was opposite, rather than wanting people to be able to do
different things, we want people to see exactly the same thing.

One important fact I have discovered is that the Play Framework by default will
reuse the same instance of a controller for all requests. This in part has
caused some of the issues I have experienced recently. In Tic Tac Toe, I was
experiencing a "bug" where on page refresh in one browser it would occasionally
display the game state from a game running in another browser!

```java
public class HomeController extends Controller {

    private Board board;
    private Game currentGame;
    private WebInterface ui = new WebInterface();
    private Map<String, Board> boardMap = new HashMap<>();
    private Map<String, Game> gameMap = new HashMap<>();

    public Result play(Option<Integer> position) {
        board = boardMap.get(session("board_id"));
        currentGame = gameMap.get(session("game_id"));
        if (position.isDefined() && !ui.nextMoveIsValid(board, currentGame.getCurrentPlayer().choosePosition(board))) {
            ui.makeNextMove(currentGame, board, position.get());
            return ok(game.render("Let's Play!", board.getCells(), ui.endGame(board), currentGame.isOver(board)));
        }
        ui.makeNextMove(currentGame, board, currentGame.getCurrentPlayer().choosePosition(board));
        return ok(game.render("Let's Play!", board.getCells(), ui.endGame(board), currentGame.isOver(board)));
    }
```

In the example above, the play method, is called after a GET request to
"/play". The first two lines of the method were meant to retrieve the correct
game from the session, process a move on the board and then display it. After
a long time spent trying to work out the fault, I ended up sending the hashCode
of the board to the view, so that I could see whether the board that was being
displayed was the same board in a different state or actually the other board
being played.

```java
    public Result play(Option<Integer> position) {
        board = boardMap.get(session("board_id"));
        currentGame = gameMap.get(session("game_id"));
        if (position.isDefined() && !ui.nextMoveIsValid(board, currentGame.getCurrentPlayer().choosePosition(board))) {
            ui.makeNextMove(currentGame, board, position.get());
            return ok(game.render("Let's Play!", board.getCells(), ui.endGame(board), currentGame.isOver(board), Integer.toString(board.hashCode())));
        }
        ui.makeNextMove(currentGame, board, currentGame.getCurrentPlayer().choosePosition(board));
        return ok(game.render("Let's Play!", board.getCells(), ui.endGame(board), currentGame.isOver(board), Integer.toString(board.hashCode())));
    }
```

The modified method above revealed that in fact, on a page refresh with two
games being played at the same time, occaionally the value of board that was
used in the return methods, was different to the one being retrieved from the
session. The culprits in the end were the fields at the top of the controller:

```java
public class HomeController extends Controller {

    private WebInterface ui = new WebInterface();
    private Map<String, Board> boardMap = new HashMap<>();
    private Map<String, Game> gameMap = new HashMap<>();

    public Result play(Option<Integer> position) {
        Board board = boardMap.get(session("board_id"));
        Game currentGame = gameMap.get(session("game_id"));
        if (position.isDefined() && !ui.nextMoveIsValid(board, currentGame.getCurrentPlayer().choosePosition(board))) {
            ui.makeNextMove(currentGame, board, position.get());
            return ok(game.render("Let's Play!", board.getCells(), ui.endGame(board), currentGame.isOver(board), Integer.toString(board.hashCode())));
        }
        ui.makeNextMove(currentGame, board, currentGame.getCurrentPlayer().choosePosition(board));
        return ok(game.render("Let's Play!", board.getCells(), ui.endGame(board), currentGame.isOver(board), Integer.toString(board.hashCode())));
    }
}
```

By removing these variables from the controller, and keeping each controller method
stateless, I was able to remove the bug and cleanly allow multiple people to
play a game. The maps used to store and retrieve the session content are
suitable to sit across the single controller instance, since it is
a requirement that they are consistent for different users.

Now on to LunchMan, where a similar problem was occuring, but again the
solution was not obvious for a long time. The issue we were experiencing was
that on page refresh the list on our homepage would double in size. And we
spent a lot of time checking each method to see if it was responsible for the
duplication:

```java
public class HomeController extends Controller {

    private String apprenticeCSV = "/Users/molliestephenson/Java/LunchMan/csvs/apprentices.csv";
    private String scheduleCSV = "/Users/molliestephenson/Java/LunchMan/csvs/schedule.csv";
    private List<FridayLunch> schedule = new ArrayList<>();
    private Rota rota = new Rota(4, LocalDate.now());


    public Result index() {
        List<Apprentice> apprentices = new ArrayList<>();
        try {
            CSVHelper.createApprenticesFromCSV(apprentices, apprenticeCSV);
            List<FridayLunch> loadedSchedule = createScheduleFromCSV(schedule, scheduleCSV);
            rota.setSchedule(loadedSchedule);
            rota.updateSchedule(apprentices);
            CSVHelper.saveRotaToCSV(rota.getSchedule(), scheduleCSV);
        } catch (Exception ex) {
            ex.printStackTrace();
        }
        return ok(index.render("LunchMan", rota.getSchedule()));
    }
}
```
All the methods in the `try` statement, had the potential to be responsible for
the duplication in the list. However, it was the private field `schedule` that
was the ultimate cause of the problem. Since it's state was being maintained,
when it was passed into `createScheduleFromCSV` it was simply appended to,
rather than written from fresh. Again, the fact that the HomeController is only
instantiated once is relevant to this problem.

This error was solved by passing a `new ArrayList<>()` into the method instead
of the schedule variable.

Further refactoring is required to improve the code here, but it is great that
we managed to finally deliver the story.


