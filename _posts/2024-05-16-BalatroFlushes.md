---
layout: post
title: "How Many Balatro Flushes Should You Expect To Play"
author: "Rohan Rao"
categories: Games
image: balatro_flush_thumbnail.001.png
---

<style>
    @font-face {
        font-family: 'CustomFont';
        src: url('assets/m6x11.ttf') format('truetype');
    }
</style>

<div style="font-family: 'CustomFont'; font-size: 20px;">
<h1> Flushes, Hands, and Discards In Balatro</h1>

Balatro is a a poker-inspired roguelike deck builder thats fairly popular right now. In the game you have a hand of cards and during each turn you can play a poker <font color='blue'>hand</font> up to 5 cards (scoring points based on the poker hand that the cards you play make), or <font color='red'>discard</font> up to 5 cards. After either action you redraw up to your hand size. You have a certain number of <font color='blue'>hands</font> and <font color='red'>discards</font> available to you.

<br>
Here's a little animation that shows an example of a starting hand, then a <font color='red'>discard</font>, then <font color='blue'>playing a hand</font>.
<br> 
<img src="assets/img/balatro.gif" width="750" align="center">

Generally as the game goes on you collect items that improve the way that certain hand types score, and modify the composition of your deck. Eventually decks might specialize to try and play as many <font color='blue'>hands</font> of the relevant high-scoring type as possible
<br>
To simplify things we'll think about a deck which tries to play a lot of flushes

<h2> The Main Question </h2>

With optimal use of <font color='blue'>hands</font> and <font color='red'>discards</font>, how many flushes should we expect to play?
This seemed like an interesting thing to look at because flush builds in Balatro generally are self contained (so it seems more ok to ignore other hand types) and lots of decisions in the game revolve around improving the consistency and number of flushes you are able to play in a round.

There are 3 main deck parameters that items in the game can change
<br> <br>
(1) <font color='blue'>hands</font> played (starts at 4)
<br>
(2) <font color='red'>discards</font> allowed (starts at 3)
<br>
(3) hand size (base 8)
<br><br>
Mainly vouchers, stakes, and boss blinds interact with these buut there are also some jokers that modify these aspects of your deck.
<br><br>
Another aspect of your deck that can be modified is the suit of cards in your deck and cards that improve how easy it is to get a flush (smear joker / checker deck, four fingers, tarot cards).
<br><br>
I won't simulate all the possibilities but I'll pick enough to paint a picture

<h2> Expected Number of Flushes </h2>

</div>    

<span  style="font-family: 'CustomFont'; font-size: 20px;"> if we just think of our hand  $$h$$ as a vector of size 4 (each entry representing the number of that suit we have, and similarly think of our remaing deck $$r$$ and our action $$a$$ (either discard or play) as vectors as well (limiting $$0 < \|a\| \leq 5$$). </span>

<span  style="font-family: 'CustomFont'; font-size: 20px;"> We can think of $$\mathbb{V}((h,r))$$ or value function of that game state as the expected number of flushes being played given optimal play. We can also define a Q-function $$\mathbb{Q}((h,r), a)$$ which is the expected number of flushes played in the game if you take action $$a$$ at state $$(h,r)$$. These are related below (assuming we have infinite <font color='blue'>hands</font> and <font color='red'>discards</font>):  </span>

<span style="font-family: 'CustomFont'; font-size: 20px;"> $$ \mathbb{Q}((h,r), a) = \mathbb{1}_{\max{a} = 5} + \sum_{c} p(c,r)\mathbb{V}((h-a+c, r - c)) $$ </span>
<br><br>
<span style="font-family: 'CustomFont'; font-size: 20px;"> $$ \mathbb{V}((h,r)) = \max_{a}{\mathbb{Q}((h,r),a)} $$</span>
<br><br>
<span style="font-family: 'CustomFont'; font-size: 20px;">$$p(c,r) = \binom{|r|}{|c|}^{-1}\prod_{i = 1}^4 \binom{r[i]}{c[i]}$$ </span>
<br><br>
<span style="font-family: 'CustomFont'; font-size: 20px;">$$c$$ is the vector of cards we draw after <font color='red'>discarding</font> or <font color='blue'>playing</font>. In the above formulation the player picks their vector of cards to play or discard $$a$$ and if $$a$$ has a flush we'll <font color='blue'> play </font> it, otherwise <font color='red'>discard</font> it. (unlike the game where you can misclick and ruin a great run). $$p(c,r)$$ is the probability of drawing $$c$$ from the remaining deck $$r$$. </span>

<span style="font-family: 'CustomFont'; font-size: 20px;"> If we have a fixed number of <font color='blue'>hands</font> and <font color='red'>discards</font> unlike the infinite case above we just need to include the <font color='blue'>plays</font> and <font color='red'>discards</font> left in $$\mathbb{V}$$ and $$\mathbb{Q}$$ which I won't write out because its verbose (but similar in principle). </span>

<span style="font-family: 'CustomFont'; font-size: 20px;"> Once we have $$\mathbb{V}((h, r))$$ for all starting possibilities of $$(h,r)$$ we can get </span>

<span style="font-family: 'CustomFont'; font-size: 20px;"> $$\mathbb{E}(flushes\_played) = \sum_{s} p(s)\mathbb{V}(s)$$ </span>
<br>

 <span style="font-family: 'CustomFont'; font-size: 20px;"> It's worth noting that winning a round just requires a player to score enough points and winning in fewer <font color='blue'>hands</font> gives you a round-end bonus and our version of optimal play functionally ignores this (we just care about the potential number of playable flushes in expectancy). </span>

 <span style="font-family: 'CustomFont'; font-size: 20px;"> The expected number of flushes gives us some information about the scoring capacity of our deck (if we went all out how much do we think we can score?), but sometimes we care about consistency (which is more of a claim about the cdf of our distribution of number of flushes). Generally you might want to know the probability that you miss hitting $$n$$ flushes. or </span>

 <span style="font-family: 'CustomFont'; font-size: 20px;"> $$\mathbb{P}(F < n)$$ </span>

 <span style="font-family: 'CustomFont'; font-size: 20px;"> Which if we know the expectation we can get a loose bound (Markov style). If $$M = h - F$$ where $$h$$ is the <font color='blue'>hands</font> you can play andf $$F$$ is the number of flushes you can play. $$M$$ is non-negative so </span>

 <span style="font-family: 'CustomFont'; font-size: 20px;"> $$\mathbb{P}(F < n) = \mathbb{P}(M \geq h - n) \leq \frac{\mathbb{E}(M)}{h - n} = \frac{h - \mathbb{E}(F)}{h - n}$$ </span>

 <span style="font-family: 'CustomFont'; font-size: 20px;"> Can also just use the vanilla form for the (pessimistic) other side </span>

 <span style="font-family: 'CustomFont'; font-size: 20px;"> $$\mathbb{P}(F \geq n) \leq \frac{\mathbb{E}(F)}{n}$$ </span>

<div style="font-family: 'CustomFont'; font-size: 20px;">

<h2> Solving the Above Computationally </h2>

Generally the above can be solved via dynamic programming (where we cache previous solutions for states and otherwise solve recursively). As written for a given hand size (lets say 8) we have an estimated number of possible states of. Estimated because we might not need to solve all these states (just the ones that we need to compute the value function for all the starting states.
</div>

 <span style="font-family: 'CustomFont'; font-size: 20px;"> $$\sum_{s} (14-s[0])(14-s[1])(14-s[2])(14-s[3]) = 3,234,330$$ </span>

 <span style="font-family: 'CustomFont'; font-size: 20px;"> One observation that we can use to further simplify things is that the value function is unchanged if you relabel the colors. In other words if we have a permutation $$\sigma(.)$$ </span>

 <span style="font-family: 'CustomFont'; font-size: 20px;"> $$\mathbb{V}((\sigma(h), \sigma(r))) = \mathbb{V}((h,r))$$ </span>

 <span style="font-family: 'CustomFont'; font-size: 20px;"> So we only need to cache one of the permutations which helps a bit - to an estimated upper bound of $$293,042$$ </span>

 <span style="font-family: 'CustomFont'; font-size: 20px;"> Now we can just simulate. </span>
<div style="font-family: 'CustomFont'; font-size: 20px;">
<h2> Hand Sizes </h2>

<h3> Default Hand Size </h3>

By default you start with 8 cards in hand. To start out we'll compute the expected flushes given some different numbers of plays and discards. By default you start out with <font color='blue'>4 hands</font> and <font color='red'>3 discards</font> but depending on the deck you are playing with you might start with (4,4) or (5,3).  (the red and blue deck). Other decks (black deck) have you start one <font color='blue'>hands</font> down (but give you another bonus)

<br><br>
Some vouchers or jokers modify these - such as:
<br><br>

<div style="display: inline-flex; width: 100%;">
    <figure width="33%">
        <img src="assets/img/juggler.png" style="width: 100px;">
        <figcaption style="font-family: 'CustomFont';text-align: center; color: blue;">Juggler</figcaption>
    </figure>
    <figure width="33%">
        <img src="assets/img/hand.png" style="width: 100px">
        <figcaption style="font-family: 'CustomFont';text-align: center; color: blue;">Grabber</figcaption>
    </figure>
    <figure width="33%">
        <img src="assets/img/nacho.png" style="width: 100px">
        <figcaption style="font-family: 'CustomFont';text-align: center; color: blue;">Nacho Tong</figcaption>
    </figure>
</div>
<br><br>
Which gives <font color='blue'>+1 Hand</font>
<br><br><br>
And also
<br>
<div style="display: flex; width: 40%;">
    <figure width="30%">
        <img src="assets/img/drunken.png" style="width: 100px">
        <figcaption style="font-family: 'CustomFont';text-align: center; color: red;">Drunkard</figcaption>
    </figure>
    <figure width="30%">
        <img src="assets/img/discard.png" style="width: 100px">
        <figcaption style="font-family: 'CustomFont';text-align: center; color: red;">Wasteful</figcaption>
    </figure>
    <figure width="30%">
        <img src="assets/img/discard2.png" style="width: 100px">
        <figcaption style="font-family: 'CustomFont';text-align: center; color: red;">Recyclomancy</figcaption>
    </figure>
</div>
<br><br>
Which gives <font color='red'>+1 Discard</font>

<br><br>
There are a lot of different <font color='blue'>hands</font> and <font color='red'>discards</font> numbers that are feasible throughout the gameplay, so for each scenario I'll pick some to solve. I'll try and pick situations that are more common or interesting in some way but some of the situations I chose to solve for were fairly arbitrary.
<br><br>
The first situation I'll solve is the default 8 card hand size deck. I included also what the expectation would be if you had an infinite number of <font color='blue'>hands</font> and <font color='red'>discards</font>.
</div>


![png](assets/img/BalatroFlushes_3_0.png)
    

<div style="font-family: 'CustomFont'; font-size: 20px;">


The above result passes a few basic sanity checks - the marginal <font color='blue'>hand</font> is better than the marginal <font color='red'>discard</font> (but here not by much). And more <font color='blue'>hands</font> + <font color='red'>discards</font> is better. 

<h3> Paintbrush</h3>

Beyond the 8 card hand size, there are a few vouchers increase your hand size - one example of them is paintbrush which increases hand size by 1 - we'll try a few of the above options but with 9 card hands. (Technically you can stack another voucher or a joker to keep growing hand size but I think these situations are probably less common in practice so I chose not to look at them for the sake of time) (though we could solve for them with the exact same approach).

</div>
    
![png](assets/img/BalatroFlushes_5_0.png)
    

<div style="font-family: 'CustomFont'; font-size: 20px;">

Unsurprisingly the extra hand helps.

<h3> Gold Stake (Or Manacle) </h3>

At Gold Stake (or when facing Manacle) you are forced to play with a decreased hand size (7). If you are playing Gold Stake you are also subject to the blue stake debuff so have to play down a <font color='red'>discard</font> . I picked a few options to try out here too (including configurations with fewer <font color='red'>discards</font>  because of the blue stake debuff).

</div>
    
![png](assets/img/BalatroFlushes_7_0.png)
    
<div style="font-family: 'CustomFont'; font-size: 20px;">

All the above results can shed some light on a few jokers that have us trade some of these parameters for others

<h3> Merry Andy and the Troubador </h3>

<div class="image-container" style="display: flex; width: 40%; margin: 0 auto;">
    <figure width="30%">
        <img src="assets/img/merry_andy.png" style="width: 100px">
        <figcaption style="font-family: 'CustomFont';text-align: center;">Merry Andy</figcaption>
    </figure>
    <figure width="30%">
        <img src="assets/img/troubador.png" style="width: 100px">
        <figcaption style="font-family: 'CustomFont';text-align: center;">Troubadour</figcaption>
    </figure>
</div>
<br><br>
- Merry Andy: -1 Hand Size +3  <font color="red"> Discards</font>
<br>
- Troubadour: -1 <font color="blue"> Hand </font> +2 Hand Size
<br><br>
From the above it looks like Merry Andy would decrease the expected number of playable flushes in the scenarios we solved. Troubadour Seems like it would increase the expected number of flushes in the above scenarios.
<br><br>
Another Joker we can solve some scenarios for is four fingers

<h2> Four Fingers </h2>

One interesting joker that affects playing flushes is Four Fingers. It makes it so that you only need 4 cards of the same suit to score a flush (and also the same for straights but we don't care about that here). We'll simulate playing with this one too.

</div>
    
![png](assets/img/BalatroFlushes_9_0.png)
    
<div style="font-family: 'CustomFont'; font-size: 20px;">

<h2> Suit Modification </h2>

Another way that players optimize their decks to play flushes is by modifying the suits of the cards in their deck.

<h3> Smear Joker / Checkered Deck </h3>
The checkered deck and the smear joker
    
 <div class="image-container" style="display: flex; width: 40%; margin: 0 auto;">
    <figure width="30%">
        <img src="assets/img/smear.png" style="width: 100px">
        <figcaption style="font-family: 'CustomFont';text-align: center;">Smear Joker</figcaption>
    </figure>

</div>   

both change your deck to having only two suits, which affects how easy it is to play a flush.
<br><br>
Below I solved for checkered deck with 8 card hand sizes.
</div>

    
![png](assets/img/BalatroFlushes_11_0.png)
    
<div style="font-family: 'CustomFont'; font-size: 20px;">

A fun note is that checkered deck with 9 card hands doesn't need a solver.
<br><br>
If you have 9 cards in hand and only 2 suits you will always be able to play a flush (one suit needs > 4 cards otherwise you can't have a 9 card hand)!
The same is true with 7 or 8 card hands if you also have four fingers.

<h3> Tarot Cards </h3>

some tarot cards modify the suits of cards in your deck. There are 4 I looked at here (I'm ignoring lovers for now).

<div class="image-container" style="display: flex; width: 50%; margin: 0 auto;">
    <figure width="25%">
        <img src="assets/img/sun.png" style="width: 100px">
        <figcaption style="font-family: 'CustomFont';text-align: center;">Sun</figcaption>
    </figure>
    <figure width="25%">
        <img src="assets/img/moon.png" style="width: 100px">
        <figcaption style="font-family: 'CustomFont';text-align: center;">Moon</figcaption>
    </figure>
    <figure width="25%">
        <img src="assets/img/stars.png" style="width: 100px">
        <figcaption style="font-family: 'CustomFont';text-align: center;">Star</figcaption>
    </figure>
        <figure width="25%">
        <img src="assets/img/world.png" style="width: 100px">
        <figcaption style="font-family: 'CustomFont';text-align: center;">World</figcaption>
    </figure>
</div>

each of these changes up to 3 cards into a particular suit. I solved for the case where your deck is unmodified and then you use one of these. I tried a configuration where youu turn 3 cards of the same suit into another suit (1), and I also tried one where cards of 3 different suits are changed into the 4th suit (2).

</div>
    
![png](assets/img/BalatroFlushes_13_0.png)
    



    
![png](assets/img/BalatroFlushes_14_0.png)
    
<div style="font-family: 'CustomFont'; font-size: 20px;">

So looks a little better in expectancy to do the first one

<h2> End </h2>

I guess nothing too surprising.
<br><br>
Looks like for the decks where its easier by default to play flushes the marginal <font color='blue'>hand</font> helps more than the marginal <font color='red'>discard</font>. For the gold stake though they are pretty much equivalent.
<br><br>
Four fingers in many reasonable cases isn't as good as Smear Joker (or checkered deck) but if you had infinite <font color='blue'>hands</font> and <font color='red'>discards </font> its better (maybe because using up only 4 cards for a flush helps in the long run?).
<br><br>
I guess the answer in general is expect to play 2-3 flushes. Sometimes more if your deck is strong otherwise less if it's weak. 

<h2> Credits </h2>
Balatro: https://balatrogame.fandom.com/wiki/Balatro_Wiki
<br><br>
Font: https://managore.itch.io/m6x11

</div>
