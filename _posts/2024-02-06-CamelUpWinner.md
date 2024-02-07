---
layout: post
title: "Whats the best CamelUp Camel"
author: "Rohan Rao"
categories: Board Games
image: camels.png
---



# Camel Up Winner Heuristics

CamelUp (https://boardgamegeek.com/boardgame/153938/camel) is a board game in which players bet on the movements of camels that move along a track in a random fashion. There are 5 Camels in the game with different colors (Yellow, Orange, Green, Blue, White). Camels start in some starting configuration on the track where each camel is on a space and camels occupying the same space are stacked on top of each other in some order. Next 5 D3's corresponding to the colors on the camels are drawn in a random order from a pyramid and when a camel's die is drawn it is rolled and the camel moves forward 1-3 spaces. When a camel moves it moves all the camels stacked on top of it. If a camel (and its stacked camels) land on an occupied space they get stacked on top of the existing camels on a space.

Heres an example of camels moving according to the dice outcomes. (the colors don't quite line up due to differences in game edition).

<img src="assets/img/camel_up_example.gif" width="1080" align="center">

Although given some initial configuration of camels its possible to compute the camel win probabilities from the $$5! \cdot 3^5$$ dice outcomes it's a bit cumbersome to bash out on the spot mid game. In this post we'll show an easy to compute way to indicate the winning camel.

### Data

First for every starting configuration of camels we compute the winners under the $$29,160$$ possible rolls and from this we get a mapping from camel configuration to camel with highest win probability.

the race track is 15 units long (allowing camels to go past the ending if they have to after the rolls). Two configurations are the same up to a translation (moving every camel stack right by 1 square is essentially the same configuration) so wlog we place a camel on square 0. Then using a stars and bars-ish type argument we end up with $$\frac{(14 + 4)!}{14!4!} = 3060$$ configurations of camels (since we fix a camel at the start we have 4 stars and 14 bars). (It's worth noting some of these configurations are very improbable or impossible to see during a game)..

Through direct simulation we can find the winner distribution for each configuration. To build some intuition here are some simulations and corresponding winner distributions


    0  ['y', 'o', 'b']
    1
    2  ['g']
    3  ['w']
    



    
![png](assets/img/CamelUpWinner_7_1.png)
    


    0  ['y', 'o']
    1  ['b', 'g']
    2  ['w']
    



    
![png](assets/img/CamelUpWinner_8_1.png)
    


    0  ['y', 'o']
    1  ['b']
    2
    3  ['g']
    4
    5  ['w']
    



    
![png](assets/img/CamelUpWinner_9_1.png)
    



Some general observations about the outcomes. Starting the race more in front of other camels seems good as does being stacked higher on top of camels.

When determining a general rule for predicting the winning camel we'll definitely want the features $$x\_pos$$ which will indicate the square that a camel is on (where we index the last place x position as 0 for simplicity), and $$y\_pos$$.

There's also the possible aspect that a camel is able to land on another camel which can carry the camel further which improves it's chance of winning in some situations.

Lets define our final rule for scoring a camel within a configuration of camels

$$score = c_1 \cdot x\_pos + c_2 \cdot y\_pos + c_3 \cdot drafting\_bonus$$

where $$drafting\_bonus$$ is the number of occupied squares in the 3 squares following a camel. We'll predict the winner to be the camel with the highest score (and come up with some tiebreak criteria - we give the win to the farthest back camel if tied). Furthermore since this is supposed to be an easy to compute score we want $$c_1,c_2,c_3$$ to be integers. Since we only need this score to score the winner higher than other camels, we probably don't need too much precision in the coefficients anyways.

To search for these we'll grid search for coefficients in $$[10 \times 10 \times 10]$$ and pick the one that maximizes performance on our simulated configurations.

After running this it turns out $$c_1 = 2, c_2 = 2, c_3 = 1$$ correctly picks the winner on the entirety of our configurations!

$$score = 2 \cdot x\_pos + 2 \cdot y\_pos + drafting\_bonus$$

is our final score.

It might be unintuitive that having camels in front of a camel would increase the score of a camel but the camel in front gets more of a bonus from a better $$x\_pos$$ than the camel in the back would get from a $$drafting\_bonus$$

### Partially Rolled Camels

Does a score like this work if some of the dice have been rolled?
We generate all the configurations and win probabilities for partially rolled configurations to test out an extended version of this score.

A natural way to extend our score is to apply the score to the un-rolled camels (where $$y\_pos$$ now represents the un-rolled camels underneath a camel).

For camels that have been rolled but are on top of an unrolled camel we can give the camel the same score as the unrolled camel (but add some epsilon so it's better).

For camels that are rolled and not on top of an unrolled camel and not in the farthest up $$x$$ position they don't have a chance at winning so we will give them $$-\infty$$ for a score.

The last case is if an unrolled camel has the largest x coordinate and is not on top of an unrolled camel.

A naive way to judge this camel ($$c$$) is to compare it to an unrolled camel at position $$(x - 2)$$ (2 is a naive estimate for how far a camel moves on it's roll)

In other words, would you rather be an unrolled camel at $$x-2$$ in the lead? or a rolled camel at $$x$$ in the lead?

The former has a 1/3 probability of going 3 spaces and there are more complicated interactions as it can land on top of camels that roll first and overtake it (and likewise camels can land on top of it before it rolls). The latter can be thought of as the former but it gets to go first but always rolls a 2.

It turns out that there are a handful of cases when you would rather be the leading (rolled) camel.

In the following cases the front camel has been rolled and the others haven't. The below examples are the only cases where the front camel is better

<img src="assets/img/camel_up_exceptions.001.jpeg" width="1080" align="center">

```
 [x, y, rolled]
[[0. 0. 0.]
 [0. 1. 0.]
 [0. 2. 0.]
 [0. 3. 0.]
 [5. 0. 1.]]
p=[0.03446502 0.10545267 0.16666667 0.28446502 0.40895062]
```

<img src="assets/img/camel_up_exceptions.002.jpeg" width="1080" align="center">

```
 [x, y, rolled]
[[0. 0. 0.]
 [1. 0. 0.]
 [1. 1. 0.]
 [1. 2. 0.]
 [5. 0. 1.]]
p=[0.0787037  0.03240741 0.17952675 0.33899177 0.37037037]
```

<img src="assets/img/camel_up_exceptions.003.jpeg" width="1080" align="center">

```
 [x, y, rolled]
[[0. 0. 0.]
 [2. 0. 0.]
 [2. 1. 0.]
 [2. 2. 0.]
 [6. 0. 1.]]
p=[0.05761317 0.03343621 0.18518519 0.35339506 0.37037037]
```

<img src="assets/img/camel_up_exceptions.004.jpeg" width="1080" align="center">

```
 [x, y, rolled]
[[0. 0. 0.]
 [3. 0. 0.]
 [3. 1. 0.]
 [3. 2. 0.]
 [7. 0. 1.]]
p=[0.04526749 0.03395062 0.1882716  0.36213992 0.37037037]
```

The pattern dosn't continue though - even the below example has the green camel having max win probability
<img src="assets/img/camel_up_exceptions.005.jpeg" width="1080" align="center">

```
 [x, y, rolled]
[[0. 0. 0.]
 [4. 0. 0.]
 [4. 1. 0.]
 [4. 2. 0.]
 [8. 0. 1.]]
p=[0.         0.03703704 0.19753086 0.39506173 0.37037037]

```

Because I exhaustively simulated all the possible outcomes, we can verify this rule works and summarize the heuristics.

Let's call the best unrolled camel $$buc$$ and lets say it has score $$s_{buc}$$. Lets call our leading rolled camel $$lrc$$. Lets give $$s_{lrc} = 2 * (x\_pos_{lrc} - 2)$$. If $$s_{lrc} = s_{buc}$$ then:

If there is one unrolled camel on $$x = 0, y = 0$$ and if there the remaining 3 camels are unrolled and all stacked on top of one another and are either on $$x = 0$$ (on top of the first camel) or $$x = 1,2,3$$, then add $$1$$ to $$s_{lrc}$$.

Maybe there is a simpler heuristic out there or a cleaner justification for why we need this seemingly unintuitive rule but it seems easy enough to remember and fits all the data.

### Conclusion (?)

We've found an easy to compute heuristic to pick the max probability camel in a camel up leg race. The actual game involves a variety of scoring payoffs many (maybe all) of which aren't completely solved by knowing the max probability camel. For example it might be reasonable to pick actions that maximize expected points which would depend on the actual win probabilities.

Nevertheless the heuristic is easy enough that it's probably useful somehow when making some intuitive estimations about camels in the game.


