---
layout: post
apprenticeship: true
title: "Ventisette"
---

# Sessions And Broken Tests

Before starting work on the new internal project for lunch orders, I had
a shortened iteration to try and pull my TTT to a close. I didn't quite get it
to the stage I wanted, but I did manage to complete one story I was aiming for,
which was "Allow multiple people to play different games at one time".

My interpretation of this story lead me to introduce sessions to my web
application. Play has some nice helper methods for adding things to the session
and reading them back, namely:

Write to session:
```java
session("game_id", board.hashCode().toString());
```

Read from session:
```java
session("game_id");
```

I had some trouble getting sessions to behave, due to an issue I have mentioned
previously about some state in my controller. In my elation at finally getting
this to work, I neglected to re-run my HomeControllerTest suite. Over the
weekend just gone, I had to buy a version of IntelliJ and in doing so wanted to
get it setup with my previous projects. On importing my TTT, I made the
discovered that none of my tests were working.

My initial assumption (initial, as in 2 hours) was that IntelliJ Ultimate
hadn't imported the project correctly. Especially since it gives you
5 different ways to import the project (SBT, Activator, Scala, Java - Play,
Scala - Play). Having dug around for far to long, I realised that it was in
fact the introduction of sessions that had broken the test suite, and not the
new version of IntelliJ.

By way of example, here is a test from my HomeContoller suite:

```java
@Test
public void testGameChoiceHvH() {
    Map form = new HashMap<String, String>();
    form.put("gameType", "0");
    HomeController homeController = new HomeController();

    Result result = invokeWithContext(Helpers.fakeRequest().bodyForm(form),
                () -> homeController.chooseGame());

    String gameId = result.session().get("game_id");
    assertTrue(homeController.getGame(gameId).getCurrentPlayer() instanceof Human);
}
```

The problem I was having, was that the final assertion was failing with
a Runtime Exception.

```bash
java.lang.RuntimeException: There is no HTTP Context available from here.
```

To give some "context" to the failure here, the method getGame uses the value
corresponding to `"game_id"` from the session to retrieve a game instance from
a HashMap on the controller instance. The failure here is notification that,
without HTTP Context, there is not session to retrieve a value from. And hence,
the game instance cannot be retrieved this way.

I was eager to fix this problem, since I felt it should be feasible to complete
it given requirements of testing authentication in the future. I could of
course, find another way to store and retrieve the game instance, using the URL
perhaps, but I was on a mission! There are a number of other methods that
implement use of the reverse routing facility built into Play. I hoped that one
of them might expose the session, in order to complete the test. One that
I tried with some success was:

```java
@Test
public void testGameChoiceHvH() {
    Map form = new HashMap<String, String>();
    form.put("gameType", "0");
    HomeController homeController = new HomeController();

    Result result = route(fakeRequest(routes.HomeController.chooseGame()).bodyForm(form));

    String gameId = result.session().get("game_id");
    assertTrue(homeController.getGame(gameId).getCurrentPlayer() instanceof Human);
}
```

However, the issue here is that the final assertion is looking for a game on
the HomeController, but since the method was called via the reverse router, the
HomeController is not actually used for the fakeRequest. So no game exists, in
the HashMap on the controller.

I have already told the story of how long I spent trying to solve this problem
over the weekend, with untold hours looking into Guice, and various other
potential sources of solution. StackOverflow didn't even have the answer for
me.

Finally, by chance one of my google searches brought up a link to the github
repo for the Play Framework. And I gave some thought to the idea of checking
the source code for a solution. Low and behold, it was right there, in plain
sight!

```java
/**
 * Calls a Callable which invokes a Controller or some other method with a Context
 */
public static <V> V invokeWithContext(RequestBuilder requestBuilder, Callable<V> callable) {
  try {
    Context.current.set(new Context(requestBuilder));
    return callable.call();
  } catch (Exception e) {
    throw new RuntimeException(e);
  } finally {
    Context.current.remove();
  }
}
```

I had already tried a number of ways of creating a context in the `@Before`
method of junit. None of which had worked, but it was now clear to see exactly
how it could be implemented, and why the `invokeWithcontext` method didn't
expose the session afterwards. The immediate solution then was:

```java
@Test
public void testGameChoiceHvH() {
    Map form = new HashMap<String, String>();
    form.put("gameType", "0");
    HomeController homeController = new HomeController();

    Http.RequestBuilder request = fakeRequest(routes.HomeController.chooseGame()).bodyForm(form);
    try {
        Http.Context.current.set(new Http.Context(request));
        homeController.chooseGame();

    } catch (Exception e) {
        throw new RuntimeException(e);
    } finally {

        assertFalse(Http.Context.current().session().isEmpty());
        String gameId = Http.Context.current().session().get("game_id");

        assertTrue(homeController.getGame(gameId).getCurrentPlayer() instanceof DelayedComputer);

        Http.Context.current.remove();
    }
}
```

Now, I am very aware that this test is not clear, I have not yet had a chance
to refactor it, but it works! So finally I can get back to green on my test
suite and I have a method for test authentication in the future!
