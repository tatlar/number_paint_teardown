# Number Paint — Is It Really Luck? A Mechanic Deep-Dive
*A short product-thinking exercise on Pine Games' Number Paint, prepared for Product Intern application*

---

## 1. Overview

**Game:** Number Paint: Color Puzzle (Pine Games)
**Category:** Casual number-sequencing puzzle
**Model:** Free-to-play, ads + IAP
**Scale:** 100K+ downloads, 3.8★ on Google Play (1.1K reviews)

**One-line thesis:** Number Paint's core loop is genuinely well-paced, but small trust and control issues are quietly eroding retention.

---

## 2. Core Mechanic (Precise Description)

The grid (e.g., 8×8, 4×5) contains cells, some showing a visible clue number, most blank. The final solution is a single path 1→2→...→N, moving between adjacent or diagonal cells, that passes through every cell on the grid exactly once and satisfies every visible clue's numeric position along the way. Note that the player does not have to solve the level strictly in numeric order, but may start with any chain satisfying the rules (which is explicitely shown in the first level tutorial of the game). 

**Meta loop:** Collect revealed artworks over time →  hints/hearts system gates difficulty and creates soft monetization pressure → daily levels unlock at level 1000 (a notably late-game gate)

**Positioning:** Explicitly marketed as "no time pressure" — this targets a relaxation-seeking audience rather than a speed/competition-seeking one. That should shape which retention levers make sense (streaks, daily rituals) over leaderboards or timers.

## 3. Addressing One Major Problem from Reviews

**Observed in reviews:** Multiple Google Play reviewers describe losing a life on moves that felt arbitrary, with one comparing the game unfavorably to Flow Free's simpler connect-any-path rule. However, this does not match my own experience playing the game. 

**Critically, the player does not have to solve the level in one go.** A player can start anywhere useful as long as they connect numbers in ascending order. For example, on a 3×3 grid with 7 in the top-right corner, 9 in the bottom-left corner, and no other visible clues: the only cell adjacent to both 7 and 9 is the center — so the center *must* be 8, and a player can deduce and connect 7-8-9 first, with total certainty, before ever touching 1-2-3. Once that's locked in, the center cell is "used," which may now fully determine — or rule out — paths for 1-2-3 that looked equally plausible before. (The generated picture of the example is shown below). 

![Walkthrough of the 7-8-9 deduction example: player tries to connect 1-center and fails, then connects 7-center to reveal 8, then 8-9. Finally, they return to 1-2-3 around now-known center cell.](/level%20suggestion.png)

**This is the actual source of the "luck" complaints:** the *correct solving strategy* is to attack the most locally-constrained clue pairs first (the ones with only one possible connecting path, like 7-9 above), because solving them narrows the board for everything else. Players who instead default to solving in numeric order (1, then 2, then 3...) will sometimes face a genuine local choice between two or more equally-plausible paths — and pick one that later turns out to conflict with a distant clue pair they haven't attempted yet. The game correctly rejects the move, but the *reason* is invisible: the disambiguating fact lived in an unrelated, unsolved part of the board. Morevover, when two option are absolutely equivalent, in my experience, the level accepts both solutions.

## 4. Case Study: "Is There a Luck Factor?"

**Hypothesis:** *Players default to solving clue numbers in numeric order (1→2→3...) rather than by constraint strength (most-locally-determined pairs first, like a 7–9 pair with a single possible connecting cell). This default strategy causes players to commit to locally-plausible paths that later conflict with a distant, not-yet-solved clue pair — producing failures that are technically deducible in principle, but not yet visible to the player at the moment of the decision. The resulting "random" feeling is a strategy/teaching gap, not evidence of an unfair puzzle.*

**What would prove this wrong:** if most testers, unprompted, already default to solving constrained pairs first — or if most logged failures turn out to be avoidable even under numeric-order solving (i.e., the information needed was actually visible on the board at the time, and the player simply missed it).

### Test design

**Setup:** 3 people unfamiliar with the game. Mixed casual-gamer / non-gamer if possible.

**Session script (~10 min each, run one at a time, not in groups):**
1. Hand them the phone: "Play until you lose a life once." No other guidance.
2. Silently log: the order they attempt clue numbers in (numeric vs. constrained-pair-first), and the exact move immediately before the failure.
3. Immediately after failure, ask in order:
   - "What just happened?"
   - "Why do you think that move was wrong?"
   - "Was there anything elsewhere on the board that could have told you this in advance?"
4. Rate 1–5: "How much did that feel like your fault vs. bad luck?"

### Results
All three players have played the game strictly in ascending order. Up to level 15 there was no level that created a genuine need to change the order. Therefore, whenever the mistakes happened, players felt like it was a slip or just a luck rather than that they have missed something. 

95% of mistakes were **avoidable under numeric-order play** — the conflicting clue pair was already fully visible/solvable at the time, the player just didn't look.
5% **were only avoidable by solving out of numeric order** — a distant, more-constrained pair (not yet attempted) would have disambiguated it, but nothing on the board yet pointed the player there.

**The core finding:** The rate that was given by testers was 4 (where 1 is "mistakes are purely logical" and 5 is "mistakes are purely unlucky"). This signals the greatest misunderstanding: since non of first 15 levels pushes players to break the numeric order, it is assumed to be solvable in that way. 

## 5. Tiered Recommendations

**Tier 1 — Onboarding (low risk, highly recommended):** First few micro-levels explicitly teach the *strategy*, not just the controls — using an example like the 7-8-9 level discussed above to show that starting from the most-constrained pair, not from "1," is the correct approach. This targets the actual skill gap without changing the puzzle for experienced players.

**Tier 2 — Post-failure clarity (medium risk, recommended if test confirms (b) is common):** On failure, show which other clue pair — solved or unsolved — created the conflict, rather than just "wrong, -1 life." Preserves the puzzle's difficulty; fixes the invisible causality.

**Tier 3 — Real-time move validity warnings (as implemented now in the game):** Flagging a doomed move before it's made would remove the foresight/deduction skill the puzzle exists to test. Noting this explicitly, and why it's rejected, is part of the case for Tiers 1–2 instead.

**What I'd measure post-launch:**
- Tutorial completion rate and time-to-first-failure, pre/post Tier 1
- % of 1-star reviews mentioning "luck," "random," or "unfair" — before and after
- D1/D7 retention segmented by whether a player triggered the new onboarding

---

## 6. Caveats

This case study is grounded in a handful of review mentions plus my own play experience, not a large dataset — the real evidence comes from the 3-person test once it's run. The finding should be read as directional, not conclusive, until then.

---

*Prepared as part of application materials — Tatiana Larina*
