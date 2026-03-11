# Bayes' Rule: Full Derivation

## Taxonomy of Probabilities

| Term | Symbol | Definition |
|------|--------|------------|
| **Prior probability** | P(A) | Your belief about the probability of state A *before* observing any new evidence. Based on historical data, domain knowledge, or subjective judgment. |
| **Likelihood** | P(B\|A) | The probability of observing evidence B *given that* state A is true. This measures how consistent the evidence is with the hypothesized state. |
| **Marginal likelihood** | P(B) | The total probability of observing evidence B across *all* possible states. Acts as a normalizing constant. |
| **Posterior probability** | P(A\|B) | Your updated belief about the probability of state A *after* observing evidence B. This is what Bayes' Rule computes. |

## Derivation from First Principles

### Step 1: Definition of Conditional Probability

The conditional probability of A given B is defined as:

```
P(A|B) = P(A and B) / P(B)        ... (1)
```

This holds whenever P(B) > 0.

Similarly, the conditional probability of B given A is:

```
P(B|A) = P(A and B) / P(A)        ... (2)
```

### Step 2: Solve for the Joint Probability

From equation (2), multiply both sides by P(A):

```
P(A and B) = P(B|A) * P(A)        ... (3)
```

### Step 3: Substitute into Equation (1)

Replace P(A and B) in equation (1) with the expression from equation (3):

```
P(A|B) = P(B|A) * P(A) / P(B)    ... (4)
```

This is **Bayes' Rule**.

### Step 4: Expand the Denominator Using the Law of Total Probability

If A can take on mutually exclusive and exhaustive states A_1, A_2, ..., A_n, then:

```
P(B) = SUM over k [ P(B|A_k) * P(A_k) ]     ... (5)
```

Substituting (5) into (4) gives the full form:

```
P(A_i|B) = P(B|A_i) * P(A_i) / SUM over k [ P(B|A_k) * P(A_k) ]    ... (6)
```

This form is most useful in practice because it expresses the posterior entirely in terms of priors and likelihoods, without requiring P(B) to be known independently.

## The Update Formula in Words

```
Posterior = (Likelihood * Prior) / Marginal Likelihood
```

Or equivalently:

```
               How well the evidence fits this state * How likely this state was before
Posterior = ---------------------------------------------------------------------------------
                      Total probability of seeing this evidence under any state
```

The denominator ensures that the posteriors across all states sum to 1.0.

## Computation Procedure

For practical problems with discrete states:

1. **List all states** A_1, A_2, ..., A_n and their prior probabilities P(A_k). Verify they sum to 1.0.
2. **Specify the likelihoods** P(B|A_k) for each state -- the probability of observing the evidence given each state.
3. **Compute joint probabilities** for each state: Joint_k = P(B|A_k) * P(A_k).
4. **Sum the joints** to get the marginal likelihood: P(B) = SUM(Joint_k).
5. **Divide each joint by the marginal** to get the posterior: P(A_k|B) = Joint_k / P(B).
6. **Verify** that the posteriors sum to 1.0.

## Worked Example

### Setup

A company is considering launching a new product. Before committing, they can commission a market research study.

**States of nature:**
- S1: High demand (the product will succeed).
- S2: Low demand (the product will fail).

**Prior probabilities** (based on historical launch data):
- P(S1) = 0.40
- P(S2) = 0.60

**Market research study outcomes:**
- Positive report (R+): The study predicts success.
- Negative report (R-): The study predicts failure.

**Test reliability (likelihoods):**
- P(R+ | S1) = 0.85 -- if demand is truly high, the study says "positive" 85% of the time.
- P(R- | S1) = 0.15 -- false negative rate.
- P(R- | S2) = 0.75 -- if demand is truly low, the study says "negative" 75% of the time.
- P(R+ | S2) = 0.25 -- false positive rate.

### Question

If the study returns a **positive report (R+)**, what is the updated probability that demand is high?

### Step-by-Step Solution

**Step 1: Identify what we need.**

We want P(S1 | R+) -- the posterior probability of high demand given a positive report.

**Step 2: Apply Bayes' Rule.**

```
P(S1 | R+) = P(R+ | S1) * P(S1) / P(R+)
```

**Step 3: Compute the joint probabilities.**

| State | Prior P(State) | Likelihood P(R+\|State) | Joint = Prior * Likelihood |
|-------|---------------|------------------------|---------------------------|
| S1 (High demand) | 0.40 | 0.85 | 0.40 * 0.85 = 0.340 |
| S2 (Low demand) | 0.60 | 0.25 | 0.60 * 0.25 = 0.150 |

**Step 4: Compute the marginal likelihood.**

```
P(R+) = 0.340 + 0.150 = 0.490
```

There is a 49.0% chance the study returns a positive report, across all possible states.

**Step 5: Compute the posteriors.**

```
P(S1 | R+) = 0.340 / 0.490 = 0.6939  (approximately 69.4%)
P(S2 | R+) = 0.150 / 0.490 = 0.3061  (approximately 30.6%)
```

**Step 6: Verify.**

```
0.6939 + 0.3061 = 1.0000  (posteriors sum to 1.0)
```

### Interpretation

Before the study, the company believed there was a 40% chance of high demand. After receiving a positive report, that belief increases to 69.4%. The positive evidence shifted the probability substantially toward the favorable state, but it did not eliminate uncertainty -- there is still a 30.6% chance the study gave a false positive.

### Completing the Analysis: Negative Report

For completeness, if the study returns a **negative report (R-)**:

| State | Prior P(State) | Likelihood P(R-\|State) | Joint = Prior * Likelihood |
|-------|---------------|------------------------|---------------------------|
| S1 (High demand) | 0.40 | 0.15 | 0.40 * 0.15 = 0.060 |
| S2 (Low demand) | 0.60 | 0.75 | 0.60 * 0.75 = 0.450 |

```
P(R-) = 0.060 + 0.450 = 0.510

P(S1 | R-) = 0.060 / 0.510 = 0.1176  (approximately 11.8%)
P(S2 | R-) = 0.450 / 0.510 = 0.8824  (approximately 88.2%)
```

A negative report drops the high-demand probability from 40% to 11.8%, strongly reinforcing the belief that demand is low.

## Key Properties

1. **Priors matter.** Two analysts with different priors will reach different posteriors from the same evidence. Bayes' Rule is only as good as the inputs.

2. **Evidence strength depends on the likelihood ratio.** The ratio P(B|A_1) / P(B|A_2) measures how much more consistent the evidence is with one state versus another. A ratio far from 1.0 means the evidence is highly informative.

3. **Multiple updates are sequential.** If you observe evidence B first and then evidence C, you can update twice: first compute P(A|B), then use that as the new prior to compute P(A|B,C) using P(C|A,B). If B and C are conditionally independent given A, this simplifies to applying each likelihood in sequence.

4. **Posteriors always sum to 1.0.** The normalization by P(B) guarantees a valid probability distribution over states after updating.

5. **With no evidence, the posterior equals the prior.** Bayes' Rule reduces to the identity when the likelihood is the same for all states (the evidence is uninformative).
