---
title: "Hand Breakdown #1"
date: 2025-12-23
draft: false
---

After reading Avi's recent blog, I got inspired to set up my own webpage and start writing about poker.

The very half-baked conception was to set some sort of measurable goal and document my personal progression as a poker player as I work towards achieving it. Still a WIP, but the current intention is to write a slightly more structured series of posts where I discuss different poker themes and my understanding of the game. I'll include some technical concepts, maybe log my results, and spend some time on meta-topics like "what should I study next", "how complex should I strive to make my game", "what's the right way to isolate improvement", etc. It's not clear to me that this will be of any interest to others, but I'm thinking of it as a poker diary that happens to be public, and as such want it to include more stuff than just hand histories. Earlier this morning I started writing out a draft of an intro page detailing this plan.

Then, as one does, I got distracted and started playing some poker. I proceeded to get totally nerd-sniped by the below hand, and all progress on the initial post was derailed. So I realized that in addition to the above, I also want to start a series of hand breakdown posts, in which I take a single hand and break it down far more thoroughly than is reasonable or time-efficient.

Here is the first installment in that series:

---
# The Hand


$100NL ACR Blitz

**Hero (BTN) open raises <span style="color: black;">A♠</span><span style="color: red;">Q♥</span> 2.5 BB**, **SB 3-bets to 11 BB**, **Hero calls**

**Flop:** (23 BB) <span style="color: black;">J♣</span> <span style="color: red;">T♥</span> <span style="color: red;">6♥</span> 

**SB bets 89 BB and is all-in**, **Hero ??**

---

# Analysis

What? Why is this a hand breakdown?

Villian seems like a spewing fish, but we don't really have a hand, so we need to fold...

**Right?**

---

## Context

Some readers will realize that this shove means villian has clearly spent time looking at the GTOWizard 3b SB vs BTN aggregated flop reports, and **is thus a reg**. I know this because I *also* value my time little enough to have spent time looking at these reports (despite the term *reg* being a generous way to describe me in this pool). Playing this size on this board has little-to-no practical effect on anything resembling winrate, but once you see it once you remember it forever, and it's fancy, so you just do it. You the reader will now also remember that **on JTX boards, the OOP 3-bettor can usually play some flop shoves.**

<figure>
  <img src="/images/hand_breakdown_1/sb_btn_3bp_flop_report.png" alt="SBvsBTN 3BP Aggregate Flop Report">
  <figcaption>If you aren't familiar with looking at these flop reports: the x-axis represents all the possible flops, the y-axis represents betting frequencies for the whole SB range. Different colors represent different sizings, with the darker colors being larger and the green being check. The darkest purple here only shows up in one spot, and it stands out! Those are the JTx boards. That's all you need to understand here.</figcaption>
</figure>


Why?

Stackoff thresholds in 3b pots are much lower than in SRPs, because the stack-to-pot-ratio is much smaller. OOP 3-bettors have a tendency to fast-play hands that are currently good but vulnerable, because the logic is that they will rarely be able to fold in the future, even on bad runouts, but they benefit by forcing hands with equity to put money in **before they know if they are ahead**

Take a simple toy example using the board above:
<span style="color: black;">J♣</span> <span style="color: red;">T♥</span> <span style="color: red;">6♥</span> 

Consider a 0.5 SPR, and the following ranges, with range 1 first to act:

Range 1: <span style="color: black;">A♠</span><span style="color: black;">J♠</span>

Range 2: <span style="color: black;">K♠</span><span style="color: black;">Q♠</span> / <span style="color: red;">A♥</span><span style="color: red;">5♥</span> / <span style="color: black;">T♣</span><span style="color: black;">9♣</span>

It turns out that simply shoving is much better than geometric size, because all of the range 2 hands have sufficient equity to call. The usual paradigm that "spreading out your bets forces your opponent to bluff catch wider" doesn't apply here, because the nuts are just not 100% equity in this spot. If the turn is a brick, all of these hands can safely fold against you. Playing 10% pot with <span style="color: black;">A♠</span><span style="color: black;">J♠</span> just because it is currently ahead of the opponent's range and that is the geometric sizing would be a large mistake. 

*Aside: I'm too lazy to calculate it, but I suspect the optimal strategy with the ranges outlined above would involve a 2-street betting plan for range 1*

## Back to the actual hand

Without having looked at these boards thoroughly before, my assumption was that the opponent's range was quite tight and centered around 2 regions:
1. Vulnerable high-equity made hands: <span style="color: black;">A♠</span><span style="color: black;">J♠</span> / <span style="color: red;">Q♥</span><span style="color: red;">Q♦</span>
2. High-equity unmade hands: <span style="color: black;">K♠</span><span style="color: black;">Q♠</span> / <span style="color: red;">A♥</span><span style="color: red;">5♥</span>

I thought hero was going to need to call a reasonably wide fraction of their range despite facing this shove, including all Jx, flush draws, and a healthy smattering of hands <= second pair. Specifically, the task in this hand is to decide whether we should prefer calling this or calling second pair. If you buy my above depiction of villian's range, this makes my exact hand (<span style="color: black;">A♠</span><span style="color: red;">Q♥</span>) look extremely intriguing as a bluff catch! It:
* Unblocks bluffs
* Does pretty well against the bluffs (74% / 57% against the two listed above)
* Blocks value

![GTOWizard Solve](/images/hand_breakdown_1/hand_solve.png)

Broad strokes, the above is pretty much correct! It seems like the exact bluff combos differ a bit, with the solver preferring hands like <span style="color: red;">A♥</span><span style="color: black;">Q♠</span>, but that feels like a minor difference.


But wait... my hand is indifferent, somewhat reasonable to call, yet **mostly a fold**. Solver seems to clearly prefer calling a number of Tx combos. What went wrong? 

![AQ hand actions](/images/hand_breakdown_1/AQ_hand_actions.png)

## The solver is an absolute beast
Everything I said above was correct, except:
> that feels like a minor difference

The solver pulled a trick on us. In iterating through the nash, it reached the same conclusion I did above, which is that <span style="color: black;">A♠</span><span style="color: red;">Q♥</span> is a pretty premium bluff catcher in this spot. **And then it went one step further**.

Despite flush draws being higher equity against our Jx pairs that are going to call, the solver decides to make sure we must call some Tx rather than favoring all our AQ by including a small frequency of shove with AK. I attempted to nodelock a sim that swaps some of the small blind's AK bluffing combos for Axhh (my original assumption of how the spot should be played) and we do indeed begin to favor AQ with a heart over Tx far more than the original solution. Neat!

# The result?

I called (I'm a station), Villian shows <span style="color: red;">A♦</span><span style="color: black;">J♠</span>

We run it twice and chop. Boring.

All's well that ends well, I guess. I suspect my call is fine (very marginal either way) against a real human opponent, and was clearly not worth doing this much analysis over. A lot of poker winrate comes from small, mundane spots, not big flashy pots where stacks go in. 

A bit unsettling to realize just how hard my "call AQ, fold Tx" strategy could have been exploited by the nodelocked solver though...


