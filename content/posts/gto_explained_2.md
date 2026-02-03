---
title: "GTO Explained #2"
date: 2026-02-02
draft: false
---

*Disclaimer: I kinda just threw something together and haven't proofread or edited much yet, apologies for any typos or other mistakes.*

---

Our trip is fast approaching, and I've been slacking on the blog posts. I started thinking about what topic I wanted to cover next, and in the process came to the conclusion that a little bit of preflop knowledge is a crucial building block for the more advanced concepts. I wrote a little bit before about how ranges influence actions, and this is no less true in real hands. The gist of my advice here is unfortunately just that it's worth spending some time memorizing hand charts, which you can look at for free on GTOWizard (I suggest using the 500NL rake structure with GTO sizings and just clicking through the game tree). You don't need to get things perfectly correct (if you're getting close you have spent too long!), but understanding and being able to replicate the broad shape of the ranges is important - the rest of this blog post will attempt to shed some light on *why* the solver is doing what it is doing preflop, and how we can use this information to make simple and effective deviations.

---

# FAQs

## Did they forgot to add limps to the game tree?

No. Limping is bad.\
Let's start by asking ourselves: what we are trying to accomplish pre-flop?

1. Are we trying to "see a cheap flop"?
    - **No.** Wellll... maybe? It's certainly true that there are some hands in our range that would prefer to limp and have the BB check. But we can't simply choose how the opponent plays! Hoping to see a cheap flop sort of relies on the goodwill of your opponent in a way you can't control. It's not a good idea to telegraph "I have a hand that wants to see a cheap flop" and just immediately put yourself in a bluff-catching spot pre-flop when they choose to raise. You can mix in some traps, but solvers tell us it's impossible to do this at a frequency that makes it actually good for your middling hands to take this limping line in the first place.
2. Are we trying to get the opponent to fold? Should we just raise really big for protection?
    - **No.** It's true that many of the hands that we **open** (aka **RFI**, or *Raise First In*) would prefer our opponents to fold, and for us to win the big blind. But there are also parts of our range that prefer the opponent to call, or even 3-bet, and again - we can't choose what our opponent does. We are also just scared about running into the top of their range and paying them way too much money when we could have gotten off more cheaply by leaving ourselves room to fold.
3. Are we trying to make the pot really big pre-flop? I want to raise really big when I have AA!
    - **No.** If you somehow magically knew that your opponent didn't have AA themselves, this wouldn't be the worst idea in the world. But the gist of it is that you expect to get raised sometimes when you open eg 2.5BB, and your best hands don't actually gain much by going bigger (they'll get raised less and also garner more folds; despite the pot being slightly bigger when you do get called, this tradeoff turns out to not be worth it.)

So what *are* we trying to do? Let's use some intuition built from my previous post to try to give a proper answer to the question. As with most concepts in poker GTO, we can arrive at the right answer by focusing on the principle of indifference!

Imagine a standard pre-flop spot as conceptually kind of similar to the final example in the previous post, where both players have a range with hands of varying strength (from 44 to AA). Like last time, we are trying to find a Nash. Instead of OOP playing a measured strategy where they bet their good hands, check their medium hands, bluff sometimes, and sprinkle in some traps, *they are forced to bet 1 BB all the time, blind, without checking their cards!*

What should we do as IP?

Well, OOP clearly just did something kind of dumb... what exactly did they mess up?

First of all, there was nothing in the pot when they made their bet. So they didn't have any incentive to "bluff" with their weaker hands. Putting in money with the equivalent of 44 feels like a mistake. Second, they bet with a bunch of medium strength hands that we can now try to force into a bluff catching spot. They also put in money with their strongest hands, which... actually seems good for them and not easy for us to punish. I suppose we should focus on the first two points. What should our response be?

We want to force them into a bluff-catching spot if we can. When we do that, we win **the whole current pot** which in this case is 1BB (there's also the SB, but let's just ignore that for now). The key is that we want to do this as cheaply as possible, because there's some "cost" associated when we run into AA or KK (not just from the BB, but from the other players in between us as well!). So we want to pick a size that maximizes on this tradeoff of making the opponents indifferent with a lot of their range and doesn't lose too much money to AA. Solvers claim this is 2-2.5 BB When *we* have the top of our range, we still aren't that sad. Sometimes the opponent is going to 3b us and build the pot for us, oftentimes with a bluff!

--- 

## Why is the solver so tight as UTG and so wide as the BTN?

When we open UTG in a 6-handed game, there are 5 people who might have really good hands and 3b us. Oftentimes we will have to fold when we get 3b against, and this is really bad for our EV (we lost our whole open size). When we are on the BTN and open, there are only 2 people who might have really good hands and 3b us! 2 < 5, so we can open wider! This is the same reason why we open a bit smaller early position and can start opening a bit larger as we get later and later.

---

## There don't seem to be a lot of cold calls. Why?

**Cold-call**: A call that does not close the action (no idea if this is the precise definition). Most commonly pre-flop these are either a non-BB call vs an open, or a call vs a 3b that does not come from the initial raiser.

Cold-calls are conceptually kind of similar to limps, in that they telegraph your hand strength a bit and force you into bluff-catching spots. Again, the tradeoff of playing these cold-call ranges and building in some traps just isn't worth it according to the solver, with the notable exception of the BTN and SB having large flatting ranges vs opens (other positions also mix these in as you take the rake towards 0). You lose very little EV by simply playing raise-or-fold from all positions other than the big blind, and that is the approach I would encourage readers of this blog to take. These spots are complicated and, in my opinion, not worth the study time to build intuition in. I myself am extremely understudied in these spots.

---

## I noticed that the SB open size is a bit bigger, and the SB/BB 3b sizes are also large. Why?

Being OOP is generally bad for EV, and this effect is magnified as the SPR grows larger and larger. The basic reason for this is that being able to check behind with your medium strength hands and just see a new street (or on the river, end the hand) is really good. When you're OOP, you have to check and then potentially face a bet, which can often force you into 0-EV bluff-catching spots. The frequency at which you are forced into bluff catching spots is a function of how often the opponent is bluffing, which is a function of how large their bet size is, which is a function of how large the SPR is.

So when you are OOP, you really want to cut down the SPR! We want to go as large as reasonably possible, without going SO big such that we allow the opponent's nutted hands to print EV. Remember, we are trying to force their range into indifference. If you are attempted to intepret this as "I'll just shove when I'm OOP with a good hand", recall the example from the previous post where Oscar had a small fraction of AA in his range, and how much Ivey lost by betting 10x the pot versus the comparatively moderate 3x pot. A very rough 3b heuristic is 3x in-position and 5x out of position, although it depends on the stack sizes and this shouldn't be treated as gospel.

---

## What's going on with this BB vs BTN 3b range? Is that right?

Another fascinating thing that solvers taught us about poker! The BB 3b range is what we call **polar**, in that it contains a mixture of both very strong and very weak hands (along with some draws, which have mediocre equity right now but will often resolve to either very strong or very weak as the board runs out). So why is the BB 3b range so polar, whereas the SB vs BTN 3b range is so **linear**?

We have another viable option that we didn't really have in previous positions, which is to call. We're getting a good price (because we already put 1BB in), and we're closing the action, both of which are important points. When we re-open the action by 3-betting, we give the opponent the chance to 4-bet us and put us in bluff-catching spots. We try to make this less painful by 3-betting a mixture of really good hands that are almost "traps" in the sense that they are happy to face a 4-bet, and really terrible hands that were close to 0 EV to begin with and don't give away as much equity by 3-betting and then having to fold. It's interesting to note that vs this polar BB 3b range, the BTN doesn't actually 4b that much, instead preferring to "play the bluff-catching node" in-position, with a larger SPR, and hoping to win more EV against the big-blind's bluffs. 

---

## How large should I 4b?

4b sizing is mostly just a function of some of the things mentioned above! The OOP player has an incentive to reduce the SPR, while the IP player has an incentive to keep it deep, so OOP 4-bets are larger. You also have more incentive to 4b against a linear range than a polar range. Separately, the goal is to make a large part of their range indifferent. You shouldn't be worried about going bigger with your nutted hands, because you should expect to face raises and/or be able to build the pot post-flop sufficiently often even when you use smaller sizes.

---

# Theoretical Preflop Deviations

Non-GTO point: When you are much better than your opponent, you should be more willing to open wider, call wider, and just generally go out of your way to play hands with them. Part of your EV is coming from them making horrible mistakes like betting the middle of their range or you being able to predict how they will act in indifferent bluff-catching spots.

I feel pretty strongly, though, that even somewhat reasonable players make lots of mistakes that are easily exploitable. As you study and improve at specific spots in the game tree, it's useful to cache your initial guesses of the correct strategy. I've found that pre-studying-me exhibits a lot of the same leaks and thought process errors as my opponents! Understanding the *why* of the solver outputs and being very aware of the most likely reasons for people to make mistakes makes it a lot easier to develop counter-strategies and print EV.

## It pays to pay attention

1. In RFI opportunities, a lot of hands make <1 BB of EV (you can see this in the strategy+EV tab on GTOWizard). This implies that they're hoping for your opponent to fold. If you notice your opponent likes to call too wide preflop, these hands go down in EV, often below 0, and you should consider folding them. It feels a bit unintuitive, but if your opponent is too wide you should likely be a little tighter pre-flop. (As an analogy, what would you want to do with a bluff in a postflop spot if you knew your opponent never folded a bluff-catcher?)

2. If your opponent is opening to a much larger size (5bb), what should you do? Their strategy is bad because they are paying too much EV when they run into strong hands. You should play a little tighter, and know that you are winning EV when you do get strong hands and 3b them. Don't pay them off with marginal hands by calling! (In fact, I would probably never call from the BB vs anything larger than a 3.5-4BB open, I think the risk-reward changes such that you should just play 3b or fold).

3. If your opponent 3-bets you too small from the SB or BB (ie 3x instead of 5x) and you are IP - continue wider! Know that you will realize your equity better by being in-position, and will sort of passively punish your opponent for not forcing you out of the hand earlier by hitting the nuts more often than you should with speculative pre-flop hands.

4. Your opponent doesn't play a polar 3b BB range
    - For one, you should fold more! If they only have the nuts when they 3b you, just get out of the way. 
    - Second, you should 4b more linearly and use a larger size. The whole reason for not doing that was that you didn't have to bet a lot to make their weaker hands indifferent, but in this case you do!

5. Your opponents play lots of cold calls
    - Again, keep in mind that a lot of your hands make EV when your opponents fold. If they're folding less often, they're shifting EV from your preflop "bluffs" to your "value" and you should adjust accordingly
    - Then again, players who play a lot of cold calls are usually not the strongest and you would do well to play lots of pots against them. You should weigh these two points against each other, but in my opinion this is actually a stronger effect.

6. Against most players, 4 bets are simply the nuts, because it's pretty hard to intuit the correct GTO strategy without looking at it. Do not get sucked into calling marginal hands vs 4-bets against players who fail to find the appropriate bluffs; over-respecting your opponents is a good way to pass up on lots of free EV. Remember, exploit folds are worth as much as exploit calls/bets when determining your winrate!

## Why is the solver such a nit?

### No set-mining operations?

By set-mining operations, I'm referring to calling a low pocket pair (usually vs a 3b) pre-flop, with the understanding that most of the EV comes from the situation where we hit a set.

It's useful to understand why the solver isn't as much of a fan of this as most humans. 

1. The opponent solver doesn't get attached to hands, and can sometimes find ways to fold good hands like top pair. Admittedly, there are some human opponents where this point doesn't apply, and if you're playing one of them then you have my permission to dust off the pickaxe and head into the mines.

2. You will often face c-bets on boards where you miss and just have to fold, and the risk-reward isn't that good. You don't get to realize all your equity (what if you would have hit on the turn!), and you're usually not getting a great price pre-flop if you're only allowed to see one street. If you are in-position facing a too-small 3b (very common against weaker players), this downside doesn't apply, so you should call your pocket pair and go for it!

### Should I flat AA against a 4b pre-flop?

Honestly probably not? This is good in theory because solvers' 4-betting ranges are often very polar, so it's kind of good to continue the trap and let them blast off with bluffs post-flop. Most humans aren't that good at having 4-bet bluffs in the first place, and also aren't good at being aggressive enough compared to theory post-flop in 4-bet pots, both of which point to just shoving AA pre-flop being better than the solver thinks.

### Do I really need to fold AJo as HJ facing an UTG open?

**Yes.**

Here's the problem - 

Problem 1: When you get 4b, you are very often dominated by a hand like AKo, and you must fold. This is a disaster, because you lost the EV of your 4b, but this alone isn't *that* much of a problem.

Problem 2: A lot of EV in poker revolves around making very strong hands and building very large pots with them. When you are in a 3b pot as HJ vs UTG, it's hard for AJo to make a very strong hand. When you hit an A on the flop, you have a decent hand! But a decent hand is worth much much less EV than a very good hand, because the size of the pot you are allowed to build is much smaller. You have to show some restraint, because once you start getting to all-in type potsizes you are isolating yourself against hands like AQ in the opponent's range, and your EV will start to plummet. Also, the opponent was already tight pre-flop, so their range won't contain enough worse hands like A8 to pay you off.

Problem 3: You'll occasionally hit a straight for the nuts, but sometimes this will be shared, because the opponent will also often have an A or a J in their hand. Also, sometimes your opponent will hit a flush and you will lose a whole stack. (This is rare, but the main point is that it's already rare for you to hit the nuts, so making that even a little less good is a meaningful downside).

It feels very unintuitive, but against most non-whale players it will be losing to 3b AJo from very early positions. I picked an extreme-seeming example to drive the point home (this hand would be perfectly fine from later positions), but I think this idea is actually fairly important.

---

# Conclusion

Hopefully the above were useful as guiding questions to try to understand the solver pre-flop charts. A little bit of memorization is somewhat mandatory, but I find the application to be much easier when you can pair it with some broad-strokes intuition. Again, the most important thing here is to understand what the pre-flop incentives are and have a rough idea of the ranges going into the flop. It's usually the case at all stages in a hand that the more accurately you can visualize both ranges, the more accurately you can play the hand. We're just trying to achieve reasonable poker-playing here (we just have to try to beat some soft live games), so I think a high-level outline is more than sufficient! I'll try to crank out a few more of these over the next couple weeks to complete the crash course (I'll optimistically aim for 5 in total); we will hopefully examine some actual hands at some point soon!