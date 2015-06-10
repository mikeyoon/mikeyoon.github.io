---
layout: post
title: Thoughts on MSpec
date: 2011-04-05 23:11:40.000000000 -08:00
---
So behavior-driven-development (BDD) has a lot of traction nowadays, and it certainly is a very useful way of designing specifications and translating them into tests. In many BDD frameworks, the style is to have a specification file and some code that tests that specification. Here’s an example from Specflow.NET:
Scenario: Gutter game
Given a new bowling game
When all of my balls are landing in the gutter
Then my total score should be 0
Specflow.NET will generate some code as such:

```csharp
[Microsoft.VisualStudio.TestTools.UnitTesting.TestMethodAttribute()]
[Microsoft.VisualStudio.TestTools.UnitTesting.DescriptionAttribute("Gutter game")]
[Microsoft.VisualStudio.TestTools.UnitTesting.TestPropertyAttribute("FeatureTitle", "Score Calculation")]
public virtual void GutterGame()
{
    TechTalk.SpecFlow.ScenarioInfo scenarioInfo =
    new TechTalk.SpecFlow.ScenarioInfo("Gutter game", ((string[])(null)));
    #line 6
    this.ScenarioSetup(scenarioInfo);
    #line 7
    testRunner.Given("a new bowling game");
    #line 8
    testRunner.When("all of my balls are landing in the gutter");
    #line 9
    testRunner.Then("my total score should be 0");
    #line hidden
    testRunner.CollectScenarioErrors();
}
```csharp

Then you would have to implement a steps class that would contain the following functions:

```csharp
[Given(@"a new bowling game")]
public void GivenANewBowlingGame()
{
    _game = new Game();
}

[Then(@"my total score should be (\d+)")]
public void ThenMyTotalScoreShouldBe(int score)
{
    Assert.AreEqual(score, _game.Score);
}
```

So you can see there is a very distinct boundary between the specification and the code that implements the test. MSpec is designed to make all this a lot more convenient by taking advantage of .NET features. Here’s an equivalent example in MSpec:

```csharp
public class when_bowling_a_new_game
{
    static Game game;
    Establish context = () => game = new Game();
    Because of = () => { //Do Something };
    It should_give_me_score = () => game.Score.ShouldEqual(30);
}
```

The big difference is the amount of stuff that is required, because there is no disconnect between the spec and the implementation. This is great for developers since it removes a lot of repetitive code, but you’ll notice that mspec doesn’t allow parameters in the specs, so I had to put 30 in for the score assertion. Most likely because everything has to be compiled anyway and that wouldn’t make much of a difference from just changing the number and recompiling again.

MSpec is sort of a way for developers to write unit tests that flow more intuitively. However, I feel that if you’re going to use BDD, you might as well go all the way and use something like SpecFlow so business users can write their specs and developers can just translate them. You could use both as well, using MSpec as a replacement for something like NUnit, and Specflow for testing business rules. MSpec is certainly a lot more readable than the usual XUnit style.
