# Probability and math for trader interviews

Study notes for the drills in this folder. The job in the room is to name the model in a few seconds, then compute. Memorize the rows in the distribution table and the counting rules. Derive everything else from linearity, conditioning, or a small state diagram.

Conventions used below, because textbooks disagree:

- Geometric waiting time counts the successful trial. A fair coin until first heads has expectation 2.
- Exponential is parameterized by rate. Mean of Exp(λ) is 1/λ.
- Normal is written N(μ, σ²) with variance in the second slot.
- Log is natural log unless a base is written.

---

## The 10-second menu

Read the sentence, then pick the tool. Most interview problems are one of these.

| What the problem says | What it is | First move |
|---|---|---|
| "Expected number of …" and the trials are messy or dependent | Linearity of indicator variables | Write one indicator per object. Add the probabilities. Dependence does not matter. |
| "How many ways to line them up / assign seats / order the draws" | Permutation | Order is part of the outcome. |
| "How many hands / committees / subsets" | Combination, n choose k | The hand is the same if you reorder the cards. |
| "Repeat until the first success" | Geometric | Mean 1/p. Memoryless. |
| "Repeat until the r-th success" | Negative binomial | Mean r/p. Sum of r geometrics. |
| "n independent yes/no trials" | Binomial | Mean np, variance np(1−p). |
| "Draw without replacement from a finite deck or urn" | Hypergeometric | Combinations in the numerator, combinations in the denominator. |
| "Rare events, large n, small p, np moderate" | Poisson | Mean and variance both λ. |
| "Given what I just saw, which coin / which disease" | Bayes | Prior times likelihood. Divide by the total probability of the evidence. |
| "The next step depends only on where I am now" | Markov chain | Name the states. Write one equation per state. |
| "I can stop or continue" | Optimal stopping | Continue only when the continuation value beats the thing in your hand. |
| "At least one" | Complement | 1 minus the probability of none. |
| "A or B or both" | Inclusion | Add, then subtract the double count. |
| "Fair game, absorb at the ends" | Gambler's ruin | On a fair walk, the hit probability is linear in your starting wealth. |
| "Collect every type" | Coupon collector | n times the harmonic number Hₙ. |
| "Two iid continuous numbers, who is larger" | Symmetry | Tie has probability 0, so each side is 1/2. With dice, subtract the ties first, then split the rest. |
| "A stick, a triangle, a random chord, points dropped on an interval" | Uniform on a simplex, or geometry | Draw the sample space. The probability is an area. |
| "Unknown proportion, and I have a few samples" | Beta / uniform prior, if you state the prior | After s successes and f failures with a flat prior, the posterior mean is (s+1)/(s+f+2). |

If none of those fit, the problem is usually "define a state by the information that matters, and condition one step."

---

## Numbers worth having cold

### Fractions as decimals

| Fraction | Decimal |
|---|---|
| 1/2 | 0.5 |
| 1/3 | 0.333… |
| 1/4 | 0.25 |
| 1/5 | 0.2 |
| 1/6 | 0.1666… |
| 1/7 | 0.142857 repeating |
| 1/8 | 0.125 |
| 1/9 | 0.111… |
| 1/10 | 0.1 |
| 1/12 | 0.08333… |
| 1/16 | 0.0625 |
| 3/8 | 0.375 |
| 5/8 | 0.625 |
| 7/8 | 0.875 |
| 1/11 | 0.0909… |
| 2/3 | 0.666… |
| 3/4 | 0.75 |
| 5/6 | 0.8333… |

1/7 = 0.142857 and then the same six digits. 2/7, 3/7, … are rotations of that block: 2/7 = 0.285714…, 3/7 = 0.428571…, 4/7 = 0.571428…, 5/7 = 0.714285…, 6/7 = 0.857142….

### Small factorials and powers

| n | n! | 2ⁿ |
|---|---|---|
| 1 | 1 | 2 |
| 2 | 2 | 4 |
| 3 | 6 | 8 |
| 4 | 24 | 16 |
| 5 | 120 | 32 |
| 6 | 720 | 64 |
| 7 | 5040 | 128 |
| 8 | 40320 | 256 |
| 9 | 362880 | 512 |
| 10 | 3,628,800 | 1024 |

Also: 3⁴ = 81, 3⁵ = 243, 4³ = 64, 5³ = 125, 6³ = 216, 7² = 49, 8² = 64, 9² = 81, 11² = 121, 12² = 144, 13² = 169, 14² = 196, 15² = 225, 16² = 256, 20² = 400, 25² = 625, 2¹⁰ = 1024, 2²⁰ ≈ 1.049 million.

### Binomial coefficients you should not recompute

Pascal's rule: C(n, k) = C(n−1, k) + C(n−1, k−1), and C(n, k) = C(n, n−k).

| | k=0 | 1 | 2 | 3 | 4 | 5 | 6 |
|---|---|---|---|---|---|---|---|
| n=2 | 1 | 2 | 1 | | | | |
| n=3 | 1 | 3 | 3 | 1 | | | |
| n=4 | 1 | 4 | 6 | 4 | 1 | | |
| n=5 | 1 | 5 | 10 | 10 | 5 | 1 | |
| n=6 | 1 | 6 | 15 | 20 | 15 | 6 | 1 |
| n=7 | 1 | 7 | 21 | 35 | 35 | 21 | 7 |
| n=8 | 1 | 8 | 28 | 56 | 70 | 56 | 28 |
| n=10 | 1 | 10 | 45 | 120 | 210 | 252 | 210 |

C(52, 2) = 1326. C(52, 5) = 2,598,960. You need the first constantly (two-card draws). The second is the number of poker hands; knowing the order of magnitude is enough.

### Constants

| Symbol | Value | Where it shows up |
|---|---|---|
| e | 2.71828… | Geometric series of 1/k!, Poisson, continuous compounding, secretary problem |
| 1/e | 0.3679… | Probability of no fixed point in a large derangement; probability the optimal secretary rule gets the best |
| ln 2 | 0.693 | Doubling times, birthday approximation, 100 prisoners ≈ 1 − ln 2 |
| γ (Euler) | 0.57721… | Hₙ ≈ ln n + γ |
| π | 3.1416 | Stirling, normal density |
| √2 | 1.414 | |
| √3 | 1.732 | |
| √5 | 2.236 | |
| ln 10 | 2.303 | log₁₀ x = ln x / ln 10 |

(1 + x)ⁿ ≈ e^{nx} when x is small and n is large. (1 − p)ⁿ ≈ e^{−np} when p is small. These two lines turn a lot of "at least one" problems into a Poisson probability.

### Sums

- 1 + 2 + … + n = n(n+1)/2. In particular 1 through 100 is 5050.
- 1² + 2² + … + n² = n(n+1)(2n+1)/6. Through 8 this is 204, which is the number of squares on a chessboard.
- 1³ + … + n³ = [n(n+1)/2]².
- Geometric sum: 1 + r + r² + … + r^{n} = (1 − r^{n+1}) / (1 − r), for r ≠ 1.
- Infinite geometric sum, |r| < 1: 1 + r + r² + … = 1/(1−r). The tail starting at r¹ is r/(1−r).
- Σ_{k≥0} k r^k = r / (1−r)². This is how geometric expectations fall out.
- Harmonic number Hₙ = 1 + 1/2 + … + 1/n. H₃ = 11/6, H₆ = 49/20 = 2.45, Hₙ ≈ ln n + γ.

### Normal quantiles worth remembering

For a standard normal Z:

| Interval | Approximate probability |
|---|---|
| within 1 σ | 68% |
| within 2 σ | 95% |
| within 3 σ | 99.7% |
| P(Z ≤ 0) | 0.50 |
| P(Z ≤ 1) | 0.84 |
| P(Z ≤ 1.28) | 0.90 |
| P(Z ≤ 1.64) | 0.95 |
| P(Z ≤ 1.96) | 0.975 |
| P(Z ≤ 2.33) | 0.99 |

So a 95% two-sided interval is about mean ± 2σ, more precisely ± 1.96σ. A one-sided 95% bound is about 1.64σ.

---

## Counting: which rule the sentence is asking for

Every counting problem is four questions:

1. Are the objects distinguishable?
2. Does order matter?
3. Is replacement allowed?
4. Are there restrictions (must be adjacent, must include Alice, at most one per box)?

Answer those before you write a formula.

### The four basic counts

Draw k items from n types.

| Order matters? | Replacement? | Count |
|---|---|---|
| Yes | No | P(n, k) = n! / (n−k)! = n(n−1)…(n−k+1) |
| No | No | C(n, k) = n! / (k! (n−k)!) |
| Yes | Yes | n^k |
| No | Yes | C(n+k−1, k), and only in the "indistinguishable balls, distinguishable bins, bins may be empty" setting |

The last row is stars and bars. It is the one people apply to the wrong story. Use it for "number of positive integer solutions of x₁+…+xₙ = k" after you have translated the problem into that equation. Do not use it for dealing cards.

### Permutation versus combination, in interview language

Use a **permutation** when two different orders are different outcomes.

- Seat 5 people: 5!.
- Gold, silver, bronze from 10 runners: 10×9×8.
- A PIN of 4 distinct digits: P(10, 4).
- The sample space of a shuffled deck, when you care which card is where: 52!.
- "The probability the first card is an ace": you can compute it as 4/52 without listing permutations. Any particular position is equally likely to be any card. That symmetry is usually faster than writing 52!.

Use a **combination** when the outcome is a set.

- A 5-card hand: C(52, 5). The deal order is not part of the hand.
- A committee of 3 from 10: C(10, 3).
- "Exactly 2 heads in 4 flips": there are C(4, 2) sequences, because the *set of positions* of the heads determines the sequence, and every such sequence has the same probability. You are counting positions, which is a combination, and then each position-set corresponds to one ordered sequence.
- Number of ways to choose k aces out of 4, when building a hand: C(4, k).

The coin-flip case is the one that feels like both. The clean split is:

- If you are counting **sequences**, and different orders are different atoms of the sample space, the atoms are ordered. There happen to be C(n, k) of the ones with k heads, because choosing the positions is a combination.
- If the problem says "the probability of HTHT specifically," that is one sequence, probability p^k (1−p)^{n−k}, and you do not multiply by a choose.

### A reliable procedure for card and urn probabilities

Without replacement, from a deck or an urn:

P(a specific ordered draw) = (falling product) / (falling product of the deck sizes).

Example: two aces in a row is (4/52)×(3/51) = 1/221.

If the problem does not care about order, you can either

- count ordered draws and notice that every unordered hand is represented by k! ordered draws, which cancels, or
- go straight to combinations:

P(exactly k aces in a 5-card hand) = C(4, k) C(48, 5−k) / C(52, 5).

That fraction is the hypergeometric probability. Use it whenever you see a finite population and a draw without replacement.

With replacement, or independent rolls, the denominator does not shrink: each trial is n choices, so n^k ordered outcomes, and the binomial formula applies if each trial is success/failure.

### Restrictions: add them before you multiply

- "Alice and Bob sit together": treat the pair as one block, so you have n−1 entities to permute, and the pair can flip internally, giving 2(n−1)!.
- "Alice and Bob sit apart": total seatings minus the together seatings.
- "At least one ace": total hands minus hands with zero aces. Complement.
- "A full house" (three of one rank, two of another): choose the three-rank, choose the two-rank, choose the suits. Order of those choices is part of getting the formula right: 13 choices for the triple rank, C(4, 3) suits, then 12 choices for the pair rank, C(4, 2) suits.
- "Words from letters of MISSISSIPPI": 11! / (4! 4! 2!), dividing by the repeats of I, S, and P. When some objects are identical, divide the permutation by the factorial of each repeated group.
- Circular arrangements of n distinct people, where rotations are the same: (n−1)!. Reflections are the same only if the problem says the table has no distinguished direction; then divide by 2 as well, for n > 2.

### Ordered versus unordered in probability, the shortcut

If every outcome you are about to count is equally likely, probability is a ratio of counts, and you must count the numerator and the denominator in the **same** style. Ordered over ordered, or unordered over unordered. Mixing them is the usual wrong answer.

If the outcomes are not equally likely (a biased coin, a weighted die), stop counting and multiply probabilities along the sequences. Combinations come back only as the number of sequences that share a probability.

---

## Probability rules that replace algebra

### Axioms you actually use

- 0 ≤ P(A) ≤ 1, and P(sure thing) = 1.
- If A and B cannot happen together, P(A or B) = P(A) + P(B). For three or more disjoint events, add them all.
- P(not A) = 1 − P(A). This is the first move for "at least one," "eventually," and "someone matches."

P(A or B) = P(A) + P(B) − P(A and B), whether or not they overlap. The subtraction is the intersection you would otherwise count twice.

For three events, add the singles, subtract the pairwise intersections, add back the triple intersection. That is inclusion-exclusion. Past three events, write the pattern rather than expanding it unless the intersections are easy.

### Independent versus disjoint

These are different words.

- **Disjoint** means they cannot happen together. P(A and B) = 0. If both have positive probability, they are dependent: learning that A happened tells you B did not.
- **Independent** means learning one does not move the other: P(A and B) = P(A)P(B), and equivalently P(A|B) = P(A) when P(B) > 0.

Mutually exclusive events are not independent, except in trivial cases where one of them has probability 0.

Pairwise independence of three events does not give you P(A and B and C) = P(A)P(B)P(C). If you need the triple product, you need mutual independence. In interview problems the trials are usually declared independent, and then the product is legal.

### Conditional probability

P(A|B) = P(A and B) / P(B), when P(B) > 0.

Read it as "restrict the sample space to B, then ask what fraction of that is also A."

Multiplication rule: P(A and B) = P(A) P(B|A) = P(B) P(A|B). For a chain,

P(A₁ and A₂ and … and Aₙ) = P(A₁) P(A₂|A₁) P(A₃|A₁ and A₂) …

That is the formula hiding inside every without-replacement card draw.

### Total probability

If B₁, …, Bₙ partition the world (one of them happens, and they don't overlap),

P(A) = Σ P(A | Bᵢ) P(Bᵢ).

Use this when the problem has a hidden type: which coin, which urn, whether the disease is present, what the first card was. Condition on the hidden type, then remove it.

### Bayes

P(Bᵢ | A) = P(A | Bᵢ) P(Bᵢ) / P(A),

with P(A) expanded by total probability.

In odds: posterior odds = prior odds × likelihood ratio.

If the prior odds of "biased" versus "fair" are 1:1, the posterior ranking is the same as the likelihood ranking. That is the whole content of a likelihood-list problem with a flat prior. If the priors differ, multiply each likelihood by its prior before you rank.

Worked base-rate problem, worth being able to redo in a minute. Prevalence 1%. Test is 99% sensitive (P(+|disease) = 0.99) and has a 5% false positive rate (P(+|healthy) = 0.05).

P(disease | +) = (0.99 × 0.01) / (0.99 × 0.01 + 0.05 × 0.99) = 0.0099 / 0.0594 = 1/6.

The test looks accurate and the posterior is still only one in six, because healthy people are so much more common. Interviewers push on this. Say the base rate out loud before you multiply.

### Symmetry shortcuts

- If X and Y are iid and continuous, P(X > Y) = 1/2.
- If X and Y are iid discrete, P(X > Y) = P(X < Y), and that common value is (1 − P(tie))/2.
- Two fair dice: P(tie) = 6/36 = 1/6, so P(friend strictly higher) = (1 − 1/6)/2 = 5/12. Same answer as counting 15 favorable outcomes out of 36.
- In a random permutation, each item is equally likely to sit in any position. P(a given card is in a given slot) = 1/n. Expected number of fixed points is Σ 1/n = 1, for every n ≥ 1. This is linearity. The probability of at least one fixed point tends to 1 − 1/e ≈ 0.632.
- Any particular card is equally likely to be in any position in the deck. P(first card is an ace) = 4/52 = 1/13. P(the ace of spades is on top) = 1/52.
- n passengers, first one sits at random, everyone else takes their own seat if free and otherwise sits at random. For n ≥ 2, the probability the last passenger gets their own seat is 1/2. The last seat is equally likely to be theirs or passenger 1's. The argument is a cycle: at every step the displaced passenger is equally likely to sit in seat 1 (which "closes" the problem in your favor, from the last passenger's point of view, once you track whose seat is the other live option) or in the last passenger's seat. You do not need the closed form for a first explanation; you need the 1/2 and the reason it does not depend on n.

### "At least one boy"

The numerical answer depends on what you learned, not on a proverb.

Two children, each independently boy or girl with probability 1/2. Sample space {BB, BG, GB, GG}, equally likely.

- You learn at least one is a boy. Drop GG. P(BB | not GG) = 1/3.
- You learn a specific child is a boy (the older one, or the one who opened the door, identified without reference to gender). Then it is 1/2.
- You learn at least one is a boy born on Tuesday, days independent and uniform. There are 27 equally likely gender-and-day outcomes consistent with that news, and 13 of them are two-boy families. The probability is 13/27.

If you cannot say which fact you conditioned on, you do not have a number yet. Say that, then compute the version they meant.

### Monty Hall

Three doors, one car, you pick one. The host, who knows where the car is, always opens a different door with a goat. Switching wins with probability 2/3, because switching wins exactly when your first pick was wrong, and that happens with probability 2/3.

The host's knowledge is the whole problem. If the host opens a door at random and just happens to show a goat, the probability has changed and you have to recompute. In the standard telling, he always opens a goat door on purpose.

### Broken stick

Break a stick at two points chosen uniformly at random. The three pieces form a triangle when each piece is shorter than 1/2, which is equivalent to the breaks not all lying in the same half in the wrong way. The probability is 1/4.

A usable picture: let the break points be the order statistics of two uniform[0, 1] draws, so the sample space of (ordered) breaks is a triangle of area 1/2, and the favorable region is the inner quarter of the full unordered square. Either picture gives 1/4. The interview wants the picture, then the number.

---

## Expectation, variance, and the shortcuts

### Definitions

For a discrete random variable, E[X] = Σ x P(X = x). For a continuous one, E[X] = ∫ x f(x) dx. The integral or sum runs over the support.

Var(X) = E[(X − μ)²] = E[X²] − (E[X])². The second form is how you compute it. Find E[X²], square the mean, subtract.

Standard deviation is the square root of variance. It has the same units as X. Variance has squared units, which is why Var(2X) = 4 Var(X), not 2 Var(X).

### Rules to use without re-deriving

- E[aX + b] = a E[X] + b.
- E[X + Y] = E[X] + E[Y], always, even when X and Y are dependent. This is linearity. It is the most important fact in the subject.
- E[XY] = E[X] E[Y] when X and Y are independent. Without independence you do not get to factor.
- Var(aX + b) = a² Var(X). Adding a constant does nothing to variance. Scaling by a scales variance by a².
- Var(X + Y) = Var(X) + Var(Y) + 2 Cov(X, Y).
- If X and Y are independent, Cov(X, Y) = 0, so variances add. The converse is false: zero covariance does not imply independence, except in special families such as jointly normal random variables.
- Var(X₁ + … + Xₙ) for independent summands is the sum of the variances. For iid summands it is n Var(X₁). The standard deviation therefore grows like √n, not like n. Averages have variance Var(X₁)/n.

Cov(X, Y) = E[XY] − E[X] E[Y]. Correlation is Cov / (σₓ σᵧ), and it lies in [−1, 1].

For indicators, Cov(1_A, 1_B) = P(A and B) − P(A)P(B). So the variance of a count of overlapping events is "sum of variances plus twice the pairwise covariances," and you can write every term as a probability.

### Indicators

Let 1_A be 1 when A happens and 0 otherwise. Then E[1_A] = P(A), and Var(1_A) = P(A)(1 − P(A)).

If X is the number of events among A₁, …, Aₙ that happen, then X = Σ 1_{Aᵢ}, so

E[X] = Σ P(Aᵢ),

even when the events overlap. You never need the distribution of X to get its mean.

Use this for:

- Expected number of fixed points in a permutation: n × (1/n) = 1.
- Expected number of aces in a 5-card hand: 5 × (4/52) = 5/13. (Hypergeometric mean, arrived at in one line.)
- Expected number of coupon types you still lack, or expected number of birthday collisions, when an exact distribution would be ugly.
- Expected number of times a pattern appears. Careful: overlapping patterns make the indicators dependent, which does not hurt the mean and does hurt a naive variance.

### Tail formulas for positive variables

If X takes values 0, 1, 2, …,

E[X] = Σ_{k≥1} P(X ≥ k).

If X is continuous and nonnegative,

E[X] = ∫₀^∞ P(X > t) dt.

These are often easier than summing k P(X = k). For a geometric number of trials until first success, P(X ≥ k) = (1−p)^{k−1}, and summing that geometric series returns 1/p.

### Conditioning the expectation

Law of total expectation: E[X] = E[ E[X | Y] ].

In words: condition on the thing that makes the problem easy, compute the conditional mean, then average that over the thing you conditioned on.

Law of total variance, less often needed but decisive when it is:

Var(X) = E[ Var(X | Y) ] + Var( E[X | Y] ).

A die rolled, then a coin flipped that many times: the variance of the number of heads has a "variance from the coin, given the die" piece and a "variance from not knowing the die" piece. Add them.

### A fair die, fully worked, because the arithmetic repeats

Faces 1 through 6, each probability 1/6.

E[X] = 21/6 = 7/2 = 3.5.

E[X²] = (1+4+9+16+25+36)/6 = 91/6.

Var(X) = 91/6 − (7/2)² = 35/12 ≈ 2.917.

For a discrete uniform on {1, …, n}: mean (n+1)/2, variance (n² − 1)/12. The die is n = 6.

Two independent dice: E[sum] = 7, Var(sum) = 35/6, because variances add. E[product] = (7/2)² = 49/4, because they are independent. The product of two dice is not independent of the sum; don't factor things that aren't independent.

### One reroll

You roll a d6 and may reroll once. If you reroll you must keep the second face.

A fresh roll is worth 3.5. Keep 4, 5, and 6. Reroll 1, 2, and 3. The largest face you throw back is 3.

E = (4+5+6)/6 + (1/2)×3.5 = 2.5 + 1.75 = 4.25.

The pattern generalizes. Write V for the value of optimal play from the start. After you see x, you keep x when x ≥ V_continue, where V_continue is the value of a fresh game with one fewer reroll. With one reroll, V_continue is just the mean of the die. With several rerolls, compute from the last roll backward.

---

## Named distributions

Memorize support, PMF or PDF, mean, and variance. The "use when" line is the part that wins the interview.

### Bernoulli(p)

One yes/no trial. X ∈ {0, 1}.

P(X = 1) = p, P(X = 0) = 1−p.

Mean p. Variance p(1−p). Maximum variance is 1/4, at p = 1/2.

Every indicator is Bernoulli. A binomial is a sum of iid Bernoullis.

### Binomial(n, p)

Number of successes in n independent trials, each with success probability p. Support k = 0, 1, …, n.

P(X = k) = C(n, k) p^k (1−p)^{n−k}.

Mean np. Variance np(1−p).

Use when the trials are independent and the success probability stays constant. Coin flips, with-replacement draws, "each customer buys with probability p."

If the draws are without replacement from a finite deck, this is the wrong distribution. That is hypergeometric. Binomial is the with-replacement or infinite-population version.

Sums: independent Binomial(nᵢ, p) with the **same** p add to a Binomial(Σ nᵢ, p). Different p's do not add to a binomial.

Normal approximation: when np and n(1−p) are both at least something like 10, X is roughly normal with that mean and variance. A continuity correction (asking for P(X ≤ k + 1/2) and so on) is worth a mention if you are approximating a small integer probability. For a fast interview estimate, mean and a couple of standard deviations is usually the ask.

### Geometric, interview convention

Number of trials until, and including, the first success. Support k = 1, 2, 3, ….

P(X = k) = (1−p)^{k−1} p.

Mean 1/p. Variance (1−p)/p².

Fair coin until heads: mean 2, variance 2. Die until a 6: mean 6, variance 30.

Memoryless: P(X > m+n | X > m) = P(X > n). The coin does not remember how many tails you have already seen. A light bulb that is geometric (or exponential) is not "due."

The other convention counts failures before the first success. Support starts at 0, mean is (1−p)/p, variance is the same. If a problem says "number of failures before the first win," use that one and say which you are using. If it says "how many flips until the first heads," you are in the convention of this note, and the answer is 1/p.

P(the number of trials is even), fair coin: the probabilities of 2, 4, 6, … flips form a geometric series (1/4) + (1/16) + (1/64) + … = 1/3. So P(even) = 1/3 and P(odd) = 2/3. The first flip alone is already an odd count with probability 1/2, and that is why odd is larger.

### Negative binomial

Number of trials until the r-th success, same success probability p, independent trials. It is the sum of r independent geometrics of the interview convention.

Mean r/p. Variance r(1−p)/p².

P(X = k) = C(k−1, r−1) p^r (1−p)^{k−r}, for k = r, r+1, ….

The choose is C(k−1, r−1) because the k-th trial must be a success, and exactly r−1 of the earlier trials are successes. Getting that index wrong is the standard slip. Say the last trial out loud before you write the coefficient.

"Best two out of three" and "first to r heads" are this distribution only if you always play until the r-th success. If you stop early because someone has already clinched, the length of the match is a different, smaller random variable. Read the stopping rule.

### Hypergeometric

Population of size N, of which K are "good." Draw n items without replacement. X is the number of good items in the draw. Support is from max(0, n−(N−K)) to min(n, K).

P(X = k) = C(K, k) C(N−K, n−k) / C(N, n).

Mean n K/N. This matches the indicator calculation.

Variance n (K/N) (1 − K/N) (N−n)/(N−1).

The last factor, (N−n)/(N−1), is the finite-population correction. It is 1 when you draw a single item, and it shrinks the variance when you draw a large fraction of the deck. Without replacement, you learn about what remains, so the variance is smaller than the binomial variance n p (1−p) with p = K/N.

Use for cards, urns, "lot of 100 parts, 5 defective, inspect 10." If they say "with replacement" or "each independently," switch to binomial.

### Poisson(λ)

Counts of rare events. Support k = 0, 1, 2, ….

P(X = k) = e^{−λ} λ^k / k!.

Mean λ. Variance λ. The equality of mean and variance is the fingerprint. If a count's variance is far from its mean, a Poisson is the wrong model.

Use when you have many independent opportunities, each with a small probability, and the expected count λ = np is moderate. Also use it as the exact count in a Poisson process.

Facts that come up:

- Independent Poisson(λ) and Poisson(μ) add to Poisson(λ+μ).
- Given that the sum is n, the split is Binomial(n, λ/(λ+μ)). "Given 10 events from two sources, how many came from the first?" is a binomial probability, with p proportional to the rates.
- Thinning: keep each event independently with probability p. The kept count is Poisson(λp), and it is independent of the discarded count Poisson(λ(1−p)).
- P(X = 0) = e^{−λ}. P(X ≥ 1) = 1 − e^{−λ}.

### Discrete uniform on {1, …, n}

P(X = k) = 1/n.

Mean (n+1)/2. Variance (n² − 1)/12.

A fair die is the case n = 6. A random seat, a random card position, and "I pick an integer from 1 to 100" in a market-making game are this distribution, or close to it.

### Continuous uniform on [a, b]

Density 1/(b−a) on the interval, 0 elsewhere.

Mean (a+b)/2. Variance (b−a)²/12.

Uniform[0, 1] has mean 1/2 and variance 1/12.

The sum of n iid Uniform[0, 1] random variables has mean n/2 and variance n/12. The distribution of the sum is the Irwin–Hall law; you rarely need the density. You do need this fact: the expected number of Uniform[0, 1] draws needed for the sum to exceed 1 is e ≈ 2.718. That is a classic. One proof sums the probabilities of still being under 1 after k draws, which are 1/k!.

### Exponential(λ), rate parameterization

Density λ e^{−λx} for x ≥ 0. CDF 1 − e^{−λx}. Survival P(X > t) = e^{−λx} with x = t.

Mean 1/λ. Variance 1/λ².

Memoryless: P(X > s+t | X > s) = P(X > t). Remaining lifetime does not depend on age. Among continuous distributions with a density on [0, ∞), the exponential is the memoryless one. If an interviewer says "it doesn't matter how long I've waited," they are pointing at this distribution or at the geometric.

Minimums: if Xᵢ are independent Exp(λᵢ), then min Xᵢ is Exp(λ₁+…+λₙ), and P(Xᵢ is the smallest) = λᵢ / Σ λⱼ. Two machines fail at rates λ and μ. Time to first failure is Exp(λ+μ). The probability it is the first machine is λ/(λ+μ). This is the continuous version of "which coin finishes first."

Connection to Poisson: in a Poisson process of rate λ, the number of events by time t is Poisson(λt), and the gaps between events are iid Exp(λ). If Tₖ is the time of the k-th event,

P(Tₖ > t) = P(fewer than k events by t) = Σ_{j=0}^{k−1} e^{−λt} (λt)^j / j!.

Use that identity instead of integrating the gamma density under time pressure.

### Gamma and Erlang, only what you need

The sum of k iid Exp(rate λ) random variables is Erlang, a gamma with integer shape k and the same rate. Mean k/λ, variance k/λ². It is the waiting time for the k-th Poisson event.

You do not need the general gamma density for a trader interview. You need "sums of iid exponentials, means add, variances add, and the Poisson-count identity above."

### Normal(μ, σ²)

Density (1 / (σ √(2π))) exp( −(x−μ)² / (2σ²) ).

Mean μ, variance σ². Symmetric about μ. Unimodal.

Standardize: Z = (X − μ) / σ is N(0, 1). Every probability question about a general normal becomes a question about Z.

Independent normals add to a normal, even with different means and variances. Mean adds, variance adds. This fails if they are not independent: the sum of jointly normal random variables is still normal, but the variance picks up 2 Cov.

A linear combination of independent normals is normal. Sample means of iid normals are normal, with variance σ²/n.

The central limit theorem is the reason the normal shows up when it was not assumed: a sum of many small independent pieces, none of which dominates, is approximately normal. Binomial with large np and n(1−p), Poisson with large λ, and a sample average of iid draws with finite variance all fall under this. Say "approximately," and give the mean and variance you are using.

### Lognormal, because of prices

If ln X is N(μ, σ²), then X is lognormal. Support (0, ∞). It is skewed right.

Median of X is e^μ. Mean of X is e^{μ + σ²/2}, which is larger than the median. Mode is e^{μ − σ²}.

A product of many independent positive factors tends to look lognormal, because the log of a product is a sum, and the sum wants to be normal. That is the modeling reason, not a theorem you need to prove in the room.

### Beta, because of unknown probabilities

Beta(α, β) lives on [0, 1]. Mean α / (α+β). Variance αβ / [(α+β)² (α+β+1)].

Beta(1, 1) is Uniform[0, 1].

The useful update: start with a Beta(α, β) prior on an unknown coin probability. See s successes and f failures. The posterior is Beta(α+s, β+f).

Flat prior, five draws, all successes: posterior Beta(6, 1), mean 6/7. That is the rigorous version of "I've seen five reds, what's my guess for the proportion," **if** you are willing to say the prior was uniform. State the prior. A different prior gives a different number. With a Beta(1, 1) prior the posterior mean is (s+1)/(s+f+2), sometimes called the rule of succession.

### Multinomial, one paragraph

n independent trials, each of which lands in one of r categories with probabilities p₁, …, pᵣ. The vector of counts is multinomial. Each coordinate is marginally Binomial(n, pᵢ). The coordinates are negatively dependent because they sum to n: Cov(countᵢ, countⱼ) = −n pᵢ pⱼ for i ≠ j. Mean of coordinate i is n pᵢ.

Dice problems that track several faces at once, and "given the counts, the order of the rolls is uniform among the sequences with those counts," are multinomial facts.

---

## Dice, coins, and cards

### One die

Mean 7/2, variance 35/12, as above.

P(even) = 1/2. P(even | greater than 3) = P({4, 6}) / P({4, 5, 6}) = 2/3. P(6 | even) = 1/3.

### Two dice

36 equally likely outcomes. Number of ways to get each sum:

| Sum | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 |
|---|---|---|---|---|---|---|---|---|---|---|---|
| Ways | 1 | 2 | 3 | 4 | 5 | 6 | 5 | 4 | 3 | 2 | 1 |

P(sum = 7) = 6/36 = 1/6, the most likely sum. Means of the two faces add, so E[sum] = 7. P(sum ≥ 10) = 6/36 = 1/6. P(doubles) = 6/36 = 1/6.

P(at least one 6) = 1 − (5/6)² = 11/36.

Expected maximum of two dice: the maximum equals k in 2k−1 of the 36 outcomes (1, 3, 5, 7, 9, 11 ways). E[max] = (1 + 6 + 15 + 28 + 45 + 66) / 36 = 161/36 ≈ 4.47.

### n dice, "at least one 6"

P(at least one 6) = 1 − (5/6)ⁿ.

Four dice: 1 − (5/6)⁴ = 1 − 625/1296 = 671/1296 ≈ 0.518.

### Coins

n independent flips, P(heads) = p. Number of heads is Binomial(n, p).

Fair coin:

- P(exactly k heads in n) = C(n, k) / 2ⁿ.
- P(more heads than tails in 2m+1 flips) = 1/2, by symmetry. In an even number of flips there is a tie possibility, so P(strictly more heads) = (1 − P(tie))/2.
- P(first heads on flip k) = 2^{−k}.
- You and an opponent alternate flips of a fair coin. First heads wins. You go first. Your win probability is 1/2 + (1/2)(1/4) + (1/2)(1/4)² + … = (1/2) / (1 − 1/4) = 2/3.

Runs of heads on a fair coin. Expected flips to get r heads in a row:

| r | Expected flips |
|---|---|
| 1 | 2 |
| 2 (HH) | 6 |
| 3 | 14 |
| 4 | 30 |

The formula is 2^{r+1} − 2. HT is not a run of identical faces. Expected flips to HT is 4, which is shorter than HH, because HH can overlap itself and a tail destroys progress in a different way. Compute both from states if you want the mechanism. The numbers 6 and 4 are worth memorizing so you can check the algebra.

### Pattern waiting, the state method

To find the expected flips until a pattern:

1. States are the current matched prefix of the pattern. Include a start state and an absorbing "done" state.
2. From each state, one flip moves you along the pattern, or back to the longest prefix that the new history still matches. Overlap lives in that sentence.
3. E_done = 0. For every other state, E_s = 1 + Σ p(next) E_next.
4. Solve from the start state.

HH, fair coin. State 0: no progress. State H: just saw a heads.

E_H = 1 + (1/2)·0 + (1/2)·E₀

E₀ = 1 + (1/2)·E_H + (1/2)·E₀

Solution: E₀ = 6.

HT, fair coin. From state H, another heads **stays** in state H, because the pattern's prefix is a single heads and you just saw one. A tails finishes.

E_H = 1 + (1/2)·E_H + (1/2)·0 = 2

E₀ = 1 + (1/2)·E_H + (1/2)·E₀ = 4

### A small Markov chain you should be able to solve

Token starts on A. From A, stay with probability 1/2, else go to B. From B, return to A with probability 1/2, else exit.

Let E_A, E_B be expected steps to exit.

E_B = 1 + (1/2) E_A

E_A = 1 + (1/2) E_A + (1/2) E_B

Substitute: E_A = 1.5 + 0.75 E_A, so E_A = 6.

Template, every time: one unknown per state, absorbing states contribute 0, each equation is "1 plus the average of where I go." If the question is a probability rather than an expectation, the same diagram works with P_s = average of P_next, and no added 1. Absorbing success is 1, absorbing failure is 0.

### Gambler's ruin

You have k dollars. Each bet you win 1 with probability p and lose 1 with probability q = 1−p. Stop at 0 or at N.

Probability of hitting N before 0, starting from k:

- Fair game, p = 1/2: k/N.
- Biased game, p ≠ q: [1 − (q/p)^k] / [1 − (q/p)^N].

Expected number of steps in the fair game: k(N−k).

The fair probability is the one to remember. It is linear because the walk is a martingale: the expected fortune at a bounded stopping time is the starting fortune, and at the end the fortune is N with the probability we want and 0 otherwise, so starting fortune = N × probability.

### Coupon collector

n types, each trial independent and uniform on the types. Expected trials to see all of them:

n Hₙ = n (1 + 1/2 + … + 1/n).

The proof is geometric waits. After you have k distinct types, the wait for a new one is geometric with success probability (n−k)/n, hence mean n/(n−k). Sum from k = 0 to n−1.

| n | Expected trials |
|---|---|
| 1 | 1 |
| 2 | 3 |
| 3 | 5.5 |
| 6 | 14.7 |
| n large | n ln n + γn + 1/2 + … |

Variance is n² Σ_{k=1}^{n} (1/k²) minus a smaller term; for interviews the mean is the usual ask. Σ 1/k² converges to π²/6 ≈ 1.64493, so the variance is on the order of n².

### Birthday problem

m days, n people, birthdays independent and uniform. P(at least one shared birthday) = 1 − P(all distinct), and

P(all distinct) = 1 · (1 − 1/m) · (1 − 2/m) · … · (1 − (n−1)/m),

provided n ≤ m. For m = 365 this drops through 1/2 at n = 23.

Approximation: P(all distinct) ≈ exp( −n(n−1) / (2m) ). Set the exponent equal to ln 2 and you get n ≈ √(2 m ln 2) ≈ 1.177 √m. For 365 that is about 22.5, hence 23.

The same approximation is "expected number of colliding pairs is C(n, 2)/m, and if that expected count λ is moderate and the pairs are weakly dependent, the number of colliding pairs is nearly Poisson(λ), so P(no collision) ≈ e^{−λ}."

### Cards

52 cards, 4 suits, 13 ranks. 12 face cards (J, Q, K). 4 aces. 26 red, 26 black. Bridge deals 13; poker hands are 5. You almost never need poker category probabilities. You need:

- P(ace) = 1/13. P(ace of spades) = 1/52. P(red) = 1/2. P(face) = 12/52 = 3/13.
- Two cards, both aces: (4/52)(3/51) = 1/221 ≈ 0.00452.
- Given the first is an ace, P(second is an ace) = 3/51 = 1/17.
- P(ace or heart) = 4/52 + 13/52 − 1/52 = 16/52 = 4/13. The ace of hearts was in both sets.
- A random hand's ace count is hypergeometric. Mean number of aces in 13 cards is 13 × 4/52 = 1.

Three cards: red/red, blue/blue, and red/blue. You pick a card at random and see a red face. P(the other side is red) = 2/3. There are three red faces you might be looking at, and two of them have red on the back. People say 1/2 because they count cards instead of faces.

---

## Stopping rules

Write the value of continuing, and compare it to the thing you are holding.

- One optional reroll of a d6: continue if the face is 1, 2, or 3, because 3 < 3.5 < 4. Value 4.25, worked above.
- You see offers one at a time and must take or reject forever. If the future is a known distribution and there is a cost to continue, the optimal rule is a threshold: take the first offer above a cutoff, where the cutoff equals the value of turning it down. You find the cutoff by indifference.
- Secretary problem. n candidates in random order, you see their relative ranks, you want the best, you cannot go back. Optimal policy: reject the first round(n/e) automatically, then take the next candidate who is the best so far. The success probability tends to 1/e ≈ 0.37 as n grows. For a trader interview this is a "have you seen it" problem. The derivation is a short integral or a sum; the answer they want is the 1/e and the shape of the policy.
- Dice you can reroll up to r times: go backward. The value with 0 rerolls left is 3.5. The value with k rerolls left is the expected value of "keep the face if it beats the value of k−1 rerolls, otherwise take that continuation value."

A common wrong move is to compare the face to the overall mean when you still have several rerolls left. With more rerolls in your pocket, the continuation is worth more than a fresh single roll, so the threshold rises.

---

## Approximations, bounds, and tails

Use these when an exact count is the wrong use of a minute.

### Union bound

P(A₁ or … or Aₙ) ≤ P(A₁) + … + P(Aₙ).

It needs no independence. It is tight when the events are rare and nearly disjoint, and it is useless when the right-hand side exceeds 1. Good for "show me this probability is small."

### Markov and Chebyshev

If X ≥ 0 and a > 0, P(X ≥ a) ≤ E[X] / a.

If μ and σ² are the mean and variance, P(|X − μ| ≥ t) ≤ σ² / t². Equivalently P(|X − μ| ≥ kσ) ≤ 1/k².

These are crude. Chebyshev says "at least 75% of the mass is within 2σ" (1 − 1/4 = 3/4), while a normal has about 95% there. Use them when you know only a mean, or only a mean and a variance, and someone asks for a guarantee rather than a sharp probability.

### Exponential and Poisson approximations

(1 − x) ≤ e^{−x} for all real x, and (1 − x) ≈ e^{−x} when x is near 0.

So (1 − p)ⁿ ≤ e^{−np}, and ≈ e^{−np} for small p.

P(no success in n independent trials) ≈ e^{−np}. P(at least one) ≈ 1 − e^{−np}.

If you want P(exactly k) in that rare-event regime, use Poisson(λ) with λ = np: e^{−λ} λ^k / k!.

### Stirling, when a factorial is in the way

n! ≈ √(2πn) (n/e)ⁿ.

Good enough to turn a binomial coefficient into an entropy expression, and good enough to see that C(2n, n) / 4ⁿ is on the order of 1/√(πn). You will not usually need more than that sentence.

### Binomial tail, a usable estimate

For a Binomial(n, 1/2), mean n/2, variance n/4, standard deviation √n / 2. The probability of being many standard deviations out is small, and quoting "mean ± 2 sd" as a rough 95% band is acceptable if you say it is the normal approximation.

Example: 100 fair flips. Mean 50, sd 5. A result of 70 heads is 4 sd out, which is not a plausible fair-coin outcome. You do not need the exact binomial term to say so.

---

## Continuous probability and a little calculus

Trader screens are mostly discrete. Written tests at some firms (SIG's problem-solving assessment is the one people mention) also want you comfortable with densities, one-variable calculus, and a geometric probability.

### Density and CDF

The CDF is F(x) = P(X ≤ x). It is nondecreasing, tends to 0 on the left and 1 on the right, and is right-continuous. You will not be tested on the continuity fine print. You will be tested on P(a < X ≤ b) = F(b) − F(a).

A density f satisfies f ≥ 0 and ∫ f = 1, and P(X in A) = ∫_A f. For a continuous distribution, P(X = x) = 0 for every single x. Saying "the probability it equals exactly 0.5" about a uniform[0, 1] is a zero. Intervals have probabilities; points do not.

E[g(X)] = ∫ g(x) f(x) dx. In particular E[X²] = ∫ x² f(x) dx, which is what you need for a variance.

### Differentiation and integration you should be fluent at

- (xⁿ)' = n x^{n−1}. ∫ xⁿ dx = x^{n+1}/(n+1) for n ≠ −1. ∫ dx/x = ln|x|.
- (e^{ax})' = a e^{ax}. ∫ e^{ax} dx = e^{ax}/a.
- Product rule: (uv)' = u'v + uv'. Quotient rule if you must; rewriting as a product is usually safer.
- Chain rule: d/dx f(g(x)) = f'(g(x)) g'(x).
- Integration by parts: ∫ u dv = uv − ∫ v du. This is how you get E[X] for an exponential or a gamma if you refuse the tail formula.
- Fundamental theorem: d/dx ∫_a^x f(t) dt = f(x).

To maximize a smooth function on an interval, check the critical points where the derivative is 0 and check the endpoints. Second derivative positive means a local minimum, negative a local maximum. For a probability you are often maximizing p(1−p) or a binomial likelihood; take the log first, because the maximum of the log is the maximum of a positive function, and the derivative gets simpler.

### Geometric probability

Drop points uniformly in a region. The probability of a subregion is its area (or length, or volume) divided by the total, provided the drop really is uniform.

Broken stick: area calculation, answer 1/4, above.

Two people arrive uniformly in an hour, independently. P(|arrival difference| < 15 minutes) = 1 − (0.75)² = 7/16. The sample space is the unit square. The band around the diagonal has width corresponding to 1/4 hour, and the two triangles outside it each have area (3/4)² / 2.

Random chord problems (Bertrand's paradox) have several answers because "a random chord" is not one distribution. If this comes up, your first sentence is which measure of randomness you are using. Do not quote a single number as if it were unique.

### Order statistics of uniforms

n iid Uniform[0, 1] samples, ordered U₍₁₎ < … < U₍ₙ₎. The k-th has mean k/(n+1).

In particular E[minimum] = 1/(n+1) and E[maximum] = n/(n+1).

The n spacings, including the ends, have the same distribution as n+1 iid exponentials divided by their sum. They are exchangeable and each has mean 1/(n+1). This is why a stick broken at n−1 uniform points has n pieces whose expected lengths are equal.

---

## Mental arithmetic under the probability

The 80-in-8 screen is its own test. These are the moves that also keep a probability answer inside 90 seconds.

### Fraction and percent reflexes

- x% of y = y% of x. So 16% of 25 is 25% of 16, which is 4.
- 15% = 10% plus half of that. 15% of 80 = 8 + 4 = 12.
- 5% is half of 10%. 2.5% is half of that.
- To divide by 5, double and divide by 10. 85 / 5 = 17.
- To multiply by 5, half the number, then multiply by 10. Or multiply by 10 and half it.
- To multiply by 25, multiply by 100 and divide by 4.
- Dividing by 4 is halving twice. Dividing by 8 is halving three times. That is why the eighths are 0.125, 0.25, 0.375, ….
- a% change followed by b% change is not (a+b)%. The multiplier is (1+a/100)(1+b/100). A 10% drop then a 10% rise lands at 0.99 of the start, not back at the start.

### Multiplication

- (a+b)(a−b) = a² − b². So 47×53 = (50−3)(50+3) = 2500 − 9 = 2491.
- (a+b)² = a² + 2ab + b². 31² = (30+1)² = 900 + 60 + 1 = 961.
- Numbers ending in 5: (10a+5)² = 100 a(a+1) + 25. So 35² = 1225, 65² = 4225, 85² = 7225.
- 12×12 through 15×15 should be instant: 144, 169, 196, 225.
- Distributing is allowed and often faster: 17×6 = 10×6 + 7×6 = 102. 24×15 = 24×10 + 24×5 = 240 + 120 = 360.
- A missing factor is a division. 66 × ? = 138.6 means ? = 138.6 / 66. Do the division. 66×2 = 132, remainder 6.6, and 6.6/66 = 0.1, so the answer is 2.1.

### Estimation when the clock is the point

For an intervals question you need a bracket, not a decimal expansion.

- Bound the thing by something you can compute. (5/6)⁴ is a bit under (0.85)⁴. 0.85² = 0.7225, and (0.72)² is a bit over 0.5, so (5/6)⁴ is a bit over 0.5 and the complement is a bit under 0.5. The true value is about 0.52. A bracket of 0.48 to 0.55 is a full-credit interval on a tight target; 0.3 to 0.7 contains the truth and scores worse because it is wide.
- If you know the answer, the interval should collapse to a point, or to a width of 1 in the last place you trust. "Hours in a week" is 168, not "150 to 200."
- Orders of magnitude: 2¹⁰ ≈ 10³, so 2¹⁰ = 1024 and 10³ = 1000. Useful for "how many times do I double to get from a thousand to a million" (about 10 more doublings, since a million is 10³ × 10³ ≈ 2²⁰).

### Series you might be asked to sum on the spot

Fair game, you win on the first turn with probability 1/2, or both players whiff and you are back at the start with probability 1/4, and so on. The sum Σ (1/4)^k from k = 0 to infinity is 1 / (1 − 1/4) = 4/3, and you usually need a piece of it, like (1/2) / (1 − 1/4) = 2/3.

If you see Σ k r^{k−1} or Σ k r^k, differentiate or start from the geometric sum. You do not need to re-derive it if you remember Σ_{k≥0} k r^k = r/(1−r)² for |r| < 1.

---

## Traps that cost a point

- **Replacement.** "Returned to the deck" is binomial or ordered-with-replacement. "Dealt" is hypergeometric or a falling product. The numerical difference between (4/52)(3/52) and (4/52)(3/51) is the whole question.
- **Order in the numerator only.** Counting unordered hands over an ordered denominator, or the reverse.
- **The last trial of a negative binomial.** The r-th success occurs on trial k only if trial k is a success. The choose is C(k−1, r−1), not C(k, r).
- **Geometric convention.** "Until the first success" includes the success, mean 1/p. "Failures before the first success" has mean (1−p)/p.
- **HH versus HT.** Same length, different expectations, 6 and 4. The overlap is the reason.
- **P(A|B) versus P(B|A).** The base-rate medical test is this trap with numbers.
- **Conditioning information.** "At least one boy" is 1/3. "The older is a boy" is 1/2. "At least one Tuesday-boy" is 13/27. Ask what was observed.
- **Disjoint and independent swapped.** If they cannot happen together, you add. If they don't inform each other, you multiply. You do not get to do both unless one probability is zero.
- **Linearity abused in the other direction.** Means always add. Variances add only when covariance is zero. Products of expectations require independence.
- **Comparing a reroll to the wrong value.** The threshold is the value of continuing, which equals the unconditional mean only when continuing means "one fresh roll and stop."
- **A wide interval that misses.** On the intervals drill, a bracket that does not contain the truth scores 0, and a bracket that contains it scores less as it gets wider. Knowing the answer and quoting 0 to 100 is a bad score. Knowing you don't, and quoting a honest range, is the skill.
- **Equal priors hiding inside a ranking.** Ranking coins by P(data | coin) is correct when the prior is flat. If one coin was much more likely before the flips, multiply by that prior before you sort.
- **"Random chord" or "random break" without a measure.** Say how the randomness is generated, then compute. Two uniform points on a stick is a clear model. "A random chord" is not, until you specify the mechanism.
- **The host in Monty Hall.** The 2/3 depends on the host deliberately opening a goat door. A host who opens at random is a different problem.
- **Integer versus continuous ties.** Continuous iid variables do not tie. Dice do. Subtract the tie probability before you split the remainder in half.
- **Off-by-one in ranges.** range(n) in Python is 0 through n−1, length n. "Integers from 1 through 100" includes both ends and has 100 terms, sum 5050. "Between 1 and 100 exclusive" is a different count. Read the endpoint.

---

## What to memorize, as a checklist

If you can produce these without looking them up, the rest of a probability interview is applying them.

**Counting.** P(n, k) and C(n, k), and which sentence means which. Falling products for cards. Divide by repetitions when objects are identical. Complement for "at least one."

**Rules.** Addition with the intersection subtracted. Multiplication along a chain of conditionals. Total probability. Bayes. Independence as a product. Linearity always. Variances add when independent. Indicator expectations.

**Distributions, with mean and variance.** Bernoulli, binomial, geometric (trials until first success), negative binomial, hypergeometric, Poisson, discrete uniform, continuous uniform, exponential (rate), normal. Beta only as "uniform prior updates by adding counts, posterior mean (s+1)/(n+2)."

**Fingerprints.** Poisson mean equals variance. Exponential and geometric are memoryless. Binomial is with replacement or independent trials. Hypergeometric is without replacement. Normal is what sums look like. Lognormal mean sits above its median.

**Small integers.** Die mean 7/2, variance 35/12. Two-dice sum table and P(sum = 7) = 1/6. HH waits 6, HT waits 4, r-in-a-row waits 2^{r+1}−2. Coupon collector n Hₙ, and the values 5.5 and 14.7. Birthday 23. Derangement probability about 1/e, expected fixed points exactly 1. Last seat on the airplane 1/2. Monty Hall switch 2/3. At-least-one-boy 1/3. Broken stick 1/4. One-reroll die 4.25, threshold 3. Fair gambler's ruin k/N. Both-aces 1/221. Medical-test style posterior can be far from the sensitivity.

**Series.** Infinite geometric sum 1/(1−r). Σ k r^k = r/(1−r)². 1+…+n = n(n+1)/2. Sum of squares formula if the written test feels like SIG. (1−p)ⁿ ≈ e^{−np}.

**Continuous.** Density integrates to 1. P(a point) = 0. Uniform[0,1] mean 1/2, variance 1/12. E[number of uniforms to exceed 1] = e. Exponential mean 1/λ, minimum of independent exponentials adds the rates. k-th uniform order statistic has mean k/(n+1). Nonnegative expectation equals the integral of the survival function.

Practice by covering the right-hand side of the 10-second menu and saying the tool before you compute. That is the skill the timed sections are scoring.
