---
title: "GTO Explained #1"
date: 2025-12-29
draft: false
---

The boys are going to Vegas! (In theory, as long as we get our act together soon and plan it). And since poker might actually be one of the least degenerate things one can do in Vegas, it makes sense to study and up and prepare to crush some fish.

Multiple people have asked me recently about how to do some cramming in a reasonably short amount of time (say 25 hours). Despite receiving this question many times, it's always been surprisingly difficult for me to answer.

I strongly believe that the best way to get better at poker *in the long run* is to just study with some kind of solver. (There's a whole separate debate here about theory vs exploit, but I won't get into it here. My personal opinion is that you need an understanding of theory anyways in order to know how to deviate, but I digress). GTOBase is a great free version, and if you're looking to be more serious GTOWizard is expensive but top-of-the-line for it's node-locking capabilities and such. But "learn to copy the solver" is an approach that is slow, takes a while to get decent payoff, and can be pretty unintuitive. You should be able to supplement this study by first learning some simple frameworks and heuristics that help you interpret solver outputs and generally think about the game. So I turned to YouTube to try to find some good materials.

I was disappointed. One unfortunate reality about the poker ecosystem is that most "pros" just aren't that good. The main thing required to buy into high stakes games is a lot of money, not a lot of skill, and so live pros are able to make a living by playing extremely simplistic strategies and owning wealthy fish. But even these pros don't have a lot of incentive to reveal their strategies (they're often playing an extremely exploitative brand of poker). The most easily accessible form of poker content comes in the form of vloggers who I doubt are even winning players (ahem Rampage). One should watch these videos only for laughs and for prime examples of what not to do. Alternatively, there's a lot more educational content from online crushers, but they are complicated and supposed a lot of background knowledge. (For examples of the type of content I'm talking about, there's this channel called Schroedinger's VPIP I recently stumbled across. I doubt that people without a lot of time in the solver would get much out of it at all, but I think the explanations and analysis are very high quality). Even ones claiming to tailor to "low stakes online players" will be targeting people not unlike myself who have spent a decent amount of time on poker. When your experience thining about EV is mostly centered around comparing the price of a box of Costco cookies to your buy-in, it's not so productive to spend a lot of time talking about blockers in some arcane spot.

Nevertheless, the trip must go on, and poker is more fun when you're winning, so I started doing a little thinking about how to introduce core game theory concepts in the most tangible way possible. The goal is to turn my friends into winning $1/$2 players with a relatively small amount of up-front investment. I think this isn't as lofty a goal as it sounds, because I truly believe the average $1/$2 player is pretty awful. The only thing you must understand in order to follow the rest of the post are the rules of poker. Let's jump in:

---

# Toy Game #1

I'm going to start with poker's most classic toy game. It's not an overstatement to say that this is the foundation of all poker GTO, and everyone trying to improve simply must understand it.

**Range 1 (IP):** AA, QQ. This means that Player 1 has either AA or QQ randomly with 50% probability.

**Range 2 (OOP):** KK

*(IP = In Position = last to act, OOP = Out Of Position = first to act)*

**SPR:** 1 

SPR = stack to pot ratio. With 2 players in the pot, this refers to the *effective stack*, which is the smaller of the two players. If player 1 has 100 chips, player 2 has 200 chips, and the pot is 100 chips, the SPR is 1. SPR is the fundamental way you should think about bet sizing and the pot in poker, and usually dominates dollars (awful) or even big-blinds.

**Board:** 22233

Let's think through the Nash for these two players.

**Player 2 starts. What should they do?**

It's pretty trivial to see that **Player 2 should start by checking**. Player 1 knows what they have and gets to act with perfect information; if they bet, player 1 simply gets to fold QQ and call AA. So by betting, Player 2 makes the pot bigger *only when they are behind*, which is bad.

**Now facing a check, what should Player 1 do?**

With AA, Player 1 should bet. They know they are winning, and Player 2 might call if they bet! Compare this to check, which wins one unit, and is the same as the "bad" case where they bet and player 2 folds (so player 1 still wins one unit).

Now what should they do with QQ? Suppose player 1 has the thought "well I know I'm behind, so I'm just going to check behind and lose." Player 1 checks behind, loses the pot, and takes a bite of their Costco cookie.

Let's pause at this iteration of strategies:

- **Player 2:** starts by checking 
- **Player 1:** Bets AA, Checks QQ
- **Player 2:** Facing a bet → Player 1 has AA → we lose → always fold.

Note that regardless, no more money is going into the pot. It's functionally the same as both players flipping over their hands at the start, with each winning 50% of the time.

The definition of a Nash is that no party can gain EV by unilaterally changing their strategy, given that the opposing strategies are fixed/known. Let's consider the scenario we outlined above more carefully.

> **Player 2: starts by checking**

This makes sense, we established earlier that betting is bad.

> **Player 1: Bets AA**

This also seems fine. We win no matter what, and can't get any MORE ev by checking behind.

> **Player 1: Checks QQ**

Wait! We win 0 units if we check, but if we bet, we know that our opponent's current strategy is to fold, so we'd win the full pot = 1 unit. So we should actually always bet QQ!

Let's reiterate our nash from Player 2's perspective now. We check and always face a bet. Given Player 1's new strategy, we know that he has QQ 50% of the time and AA 50% of the time. Our EV for a fold is 0. Our EV for a call is \\((0.5 \times 2 + 0.5 \times (-1)) = 0.5 > 0\\). So now **player 2 should always call**! (Our opponent is *overbluffing*).

It turns out that the adjustments we are making above are simply too large, but a solver will use a very similar iterative process (using something called Counterfactual Regret Minimization) to converge on an equilibrium. Here we can use the principle of indifference to produce a Nash. Simply, KK needs to call at a frequency that makes QQ indifferent between bluffing and giving up, and QQ needs to bluff at a frequency that makes KK indifferent between calling (**bluff catching**) and folding.

KK EV for folding is 0. So we want to compute QQ bluff freq such that:

\\[\text{KK}_\text{ev\_fold} = \text{KK}_\text{ev\_call}\\]

\\[0 = \text{KK}_\text{ev\_call}\\]

\\[0 = \left(\text{frac\_facing\_AA\_given\_p1\_bets} \times (-1) + \text{frac\_facing\_QQ\_given\_p1\_bets} \times 2\right)\\]

\\[0 = \frac{1}{1 + \text{QQ\_bluff\_freq}} \times (-1) + \frac{\text{QQ\_bluff\_freq}}{1 + \text{QQ\_bluff\_freq}} \times 2\\]

We can do some simple algebra here and get \\(\text{QQ\_bluff\_freq} = 0.5\\).

If player 1 was losing money by bluffing QQ though, they simply wouldn't do it. So we can compute KK call freq similarly:

\\[\text{QQ}_\text{ev\_giveup} = \text{QQ}_\text{ev\_bluff}\\]

\\[0 = \text{QQ}_\text{ev\_bluff}\\]

\\[0 = \text{frac\_facing\_fold} \times 1 + \text{frac\_facing\_call} \times (-1)\\]

And again, \\(\text{KK\_call\_freq} = 0.5\\).

Our first Nash! p2 checks, p1 bets AA always and QQ half the time, and p2 calls KK half the time.

## Framework: Minimum Defense Frequency and Bluff-To-Value Ratio

The solution we came to above is a direct function of the SPR. When Player 1 uses a pot-sized bet, Player 2 must call half the time to make their bluffs indifferent.

**Minimum Defense Frequency (MDF):** The minimum frequency a player must defend in order to make the opponent indifferent to bluffing with any two cards.

\\[\text{MDF} = \frac{\text{Pot}}{\text{Pot} + \text{Bet}} = \frac{1}{1 + \text{Bet Size as fraction of pot}}\\]

In our toy game: \\(\text{MDF} = \frac{1}{1+1} = 0.5 = 50\%\\).

This is exactly the frequency KK called at! If KK called any less, QQ could bluff always and be profitable. If it called any more, QQ would never want to bluff.

**Bluff-To-Value Ratio:** To make the caller indifferent between calling and folding, we derive a similar equation for player 1:

\\[\frac{\text{Bluffs}}{\text{Value}} = \frac{\text{Bet}}{\text{Pot + Bet}}\\]

In our toy game, with a pot-sized bet: \\(\frac{\text{Bluffs}}{\text{Value}} = \frac{1}{1+1} = \frac{1}{2}\\), so we need 1 bluff for every 2 value combos. Player 1 has 1 value combo (AA) and therefore needs 0.5 bluff combos. They can achieve this by simply betting QQ half the time.

**Examples with different bet sizes:**

- **Epsilon bet:** MDF = 100%, Bluffs:Value = 0:1 (0% bluffs, 100% value)
- **0.5x pot bet:** MDF = 67%, Bluffs:Value = 1:3 (25% bluffs, 75% value)
- **1x pot bet:** MDF = 50%, Bluffs:Value = 1:2 (33% bluffs, 67% value)
- **2x pot bet:** MDF = 33%, Bluffs:Value = 2:3 (40% bluffs, 60% value)
- **Infinite bet:** MDF = epsilon, Bluffs:Value = 1:1 (50% bluffs, 50% value)

Larger bets are paired with higher bluff frequencies, and also lower calling frequencies. But your perfectly polar hands still make more money by betting large in theory! This is a general pattern that is important to remember.

**And the solver confirms our solution:**

![toygame1_1](/images/gto_explained_1/toygame1_1.png)

![toygame1_2](/images/gto_explained_1/toygame1_2.png)

![toygame1_3](/images/gto_explained_1/toygame1_3.png)

---

# Commentary

There are a number of insightful takeaways from this relatively simple game.

1. **Bluff-catcher:** Any hand that beats all of your opponent's bluffs and loses to all of their value. Traditionally refers to hands on the river, because equities are not so binary on earlier streets (you might have a draw, for example).
    - Against balanced ranges, bluff-catchers are indifferent between calling and folding. In order for this to be true, calling must be 0 EV.
    - I put this at the top because it is the most important. The first question to ask when you have a bluff-catcher is: do I think my opponent is over or under-bluffing? If you have a clear answer to that question, stop all further thought processes and either pure call or pure fold your hand.

2. **There are no loss leaders in poker.**
    - Hands mix actions only when they are indifferent between those two actions. If KK were to start calling 1% less, QQ would snap towards bluffing **always**, not just slightly more often. You would repeat the equations I set up comparing the EV of bluffing vs giving up and discover that one is just strictly better.

3. **Some quick definitions first:**
    - **Equity:** How often a hand/range would win if both player's just flipped over their cards right now and dealt out the rest of the board.
    - **Polar:** Ranges that contain hands with either very high or very low equity vs the opponent's range. In this example, AA had 100% equity and QQ had 0%.
    - **Condensed:** Opposite of polar; ranges that contain hands with very middling equity. In this example, KK had 50% equity.

4. **Reducing your opponent's hand to a bluff-catcher is equivalent to winning the entirety of what's currently in the pot.**
    - EV is zero-sum here, and must sum to what's currently in the pot. A bluff-catcher has 0 EV, so the opponent must be making the entire pot in EV with their range when you have a bluff-catching hand.

5. **A more subtle follow-on: polar ranges have better equity realization than condensed ranges.**
    - Equity realization: \\(\text{EQR} = \frac{\text{EV\_as\_frac\_of\_pot}}{\text{equity}}\\)
    - What were the EVs of the nash strategies above? 
        - **EV:** Player 1: 0.75, Player 2: 0.25.
        - **EQR:** Player 1: 150%, Player 2: 50%.
        
        That's interesting... more on this in a bit.

6. **Strategy:**
    - Polar ranges/hands have an incentive to put money into the pot. In some sense they are at an information advantage and get to play "perfectly".
    - Condensed ranges/hands have an incentive to keep the small pot and get to showdown. They're at an information disadvantage and have to "play defense".

---

# A whole toy box: games 2-N

I came up with a few illustrative extensions of this toy game, each of which highlight different concepts. These are harder to solve in the laborious way we did above, so I'm going to use a mixture of intuition and solver screenshots as we go through them:

**2. What happens if we increase the SPR to 10 rather than 1? How do the EVs change?**

![toygame2_1](/images/gto_explained_1/toygame2_1.png)

![toygame2_2](/images/gto_explained_1/toygame2_2.png)

Player 1's EV has increased to a whopping 95% of the pot (up from 75% with SPR 1), and they now bluff QQ nearly always! Facing this bet, the optimal strategy is for Player 2 to call only ~9% of the time.

**Takeaway 1:** The larger the stack-to-pot-ratio, the more EV a polar range will have against a condensed one. It becomes more important for OOP to have "traps" as we get deeper and deeper.

**Takeaway 2:** Remember, we win the whole pot whenever we bet. When we are perfectly polar (our value hands have 100% equity), our value hands will always bet, and our bluffs will bet with a frequency that makes the opponent indifferent to calling. We're giving them worse odds when we bet larger, so we get to bluff more often, so we make more of the pot.

**Takeaway 3:** Value hands bet a size according to their equity. Bluffs simply choose the same size as the value hands.

**3. Player 2 was pretty miserable in the previous example. Maybe traps are good? Let's keep this 10 SPR, but check what happens if Player 2 protects their range and has some traps. Let's add a sliver of AA (chops) to their range. I added AA to player 2's range with 10% the weight of KK.**

We should intuitively realize that it still wouldn't make sense for Player 2 to bet AA, and should still begin with a check. What would be the point? The opponent would never call QQ!

![toygame3_1](/images/gto_explained_1/toygame3_1.png)

If you still restrict Player 1 to playing a shove or check strategy, their EV completely plummets.

![toygame3_2](/images/gto_explained_1/toygame3_2.png)

![toygame3_3](/images/gto_explained_1/toygame3_3.png)

They're not even always betting AA here, because it's only getting called by the opponent's AA, and QQ is bluffing sometimes but is forced to give up a lot. Given that the opponent needs to defend about 9% of the time vs a 10x pot shove, and they have AA 9% of the time, the bet size player 1 is using is just too big for their equity!

If you let the solver pick the size it wants, it starts using a 3x pot size bet instead. This forces the opponent to start defending KK, lets us bet AA always, and is the right size based on our equity of 90%. Our EV is back up to 72% of the initial pot (note how the tiny frequency of traps reduced this from 95% earlier, though!).

**Takeaway 1:** Why does GTO have traps? It gives us some "easy defends" and prevents the opponent's range from being perfectly polar. You'll sometimes hear poker players say things like "I thought my opponent was underprotected in this line, so I bluffed". **This isn't a valid thought process!!** There's nothing at all that stops your opponent from calling with a bluff catcher if they believe you are just overbluffing. They absolutely do not need traps to stop you from overbluffing.

There are two valid things you could say though:

1. I thought my opponent would fold enough that my bluffs make more money by betting than giving up (overfold). Self-explanatory.

2. I thought my opponent didn't have enough traps compared to theory, so I used a bigger size than the solver would. This allows me to force them into bluff-catching spots and thus win the whole pot in EV more often!

**4. Let's make this far more complicated. I'm going to give the player's equal ranges, more hands, and more bet sizing options. The river will still be a 10 SPR spot.**

Woahh this gets a lot more complicated. But at its core the solution represents something similar to what we talked about. The "nutted" hands start by trapping. The weaker but still strong hands bet a size that forces the opponent to bluff catch with hands they beat. The mediocre hands check, hoping to get to showdown, with the plan to face a bluff-catching spot if they must. The weakest hands either give up or bluff. The game tree is actually pretty deep here, but I'll just show you the first Player 2 node, and what Player 1's response looks like facing the smallest bet.

![toygame4_1](/images/gto_explained_1/toygame4_1.png)

![toygame4_2](/images/gto_explained_1/toygame4_2.png)

---

# What next?

All this was pretty neat but maybe doesn't give that much practical advice on what to do in any hands yet. Nevertheless, these concepts are so foundational that I think it makes sense as the first of the 25 hours of study, and I'll focus on more practical advice for the next 24 of them!

*Bring a man a cookie, and you feed him for a session. Teach a man to play poker, and hopefully he can lure other fish to his poker sessions with cookies **he** provides in the future.*