<!-- solve-problem:a6de8e61-3cc5-46c8-b33f-4649a42f6a8a -->
# Strategic resource-price learning

A [Tangle Solve problem](https://solve.tangle.tools/problems/a6de8e61-3cc5-46c8-b33f-4649a42f6a8a).

## What is the problem?

**Setting.** Blockchain transaction fee markets (notably EIP-1559-style and its proposed multidimensional successors) set prices for multiple computational resources (gas, calldata, state access, blob space) via an online price-update rule, with no knowledge of future demand. Users arrive over rounds t = 1, …, T, and each user has a private valuation and a resource vector (possibly with discretion over *when* to submit and *how to declare* resource usage when execution is uncertain, as in the EIP-4844 blob market where declared blob counts are committed before execution outcomes are known).

**Problem.** Existing analyses (e.g., Angeris–Diamandis–Moallemi 2024) prove near-optimality of multidimensional fee mechanisms **for specified/oblivious demand sequences**: the price update rule attains O(√T) (or polylog T) regret against a fixed benchmark. The open question: what happens when demand is **strategic** — forward-looking users who (a) shift demand intertemporally (timing games: submit now vs. later to exploit predictable price updates), (b) alter demand quantities in response to the learning rule, and (c) misreport declared resource usage when actual usage is resolved after inclusion?

**Formal targets.** Define welfare regret of the mechanism run against strategic agents, and the agents' manipulation gain (utility from strategizing minus utility from truthful/myopic play). Prove (or disprove) bounds of the form:
- Regret_strategic = O(√T) or O(T^{2/3}) under stated agent models (myopic best response, farsighted with discount δ, Nash equilibrium of the induced stochastic game);
- Manipulation gain = O(√T), i.e., strategizing is asymptotically unprofitable at per-round scale.

**Ambiguities to resolve.** The agent model (how much foresight, what information), the benchmark (optimal fixed prices vs. clairvoyant dynamic prices), and whether resource misreporting is modeled as a bandit feedback channel (declared usage ≠ realized usage) are all unspecified in the census record; pick and state them explicitly.

## Why does it matter?

Online fee mechanisms deployed in production (Ethereum's multi-EIP fee markets) are learning algorithms operating against users who demonstrably time transactions and misreport blob counts. If regret guarantees only hold for oblivious sequences, the core economic claim of multidimensional fees — near-optimal welfare without demand knowledge — is unproven in the environment it actually runs in. Clean results here would connect performative prediction with mechanism design: a template for making any online resource-allocation protocol robust to the demand it induces. Failure to resolve it leaves fee markets vulnerable to price-manipulation strategies and undermines principled design of future EIPs.

## What's already known?

- **Angeris, Diamandis, Moallemi (2024)**: multidimensional prices with a natural online update rule are essentially optimal for *multidimensional resource allocation with known/specified demand*; near-optimal regret bounds (polylog or √T-type) for the price-update controller against a benchmark. This is the baseline being extended.
- **EIP-1559 transaction fee mechanism analyses** (Roughgarden 2020/2021; also Ferreira, Moroz, et al., and Lavi, Sattath, Zohar on monopolist pricing): studied incentive compatibility of 1559's base fee, including some strategic timing behavior — but single-resource and largely myopic-agent analysis; base-fee manipulation costs are shown nontrivial but the analysis is not a regret framework.
- **Performative prediction** (Perdomo et al. 2020; repeats of risk under distribution shift, DriftSafe/deployments by Brown et al., and "performative gradient descent" work): gives stability-based tools for when retraining/price-updating fixed points exist and are findable — but no welfare-regret analysis and no agent utilities.
- **Strategic online learning / learning with incentives** (e.g., "strategic regression," Stackelberg regret, anytime revenue-maximizing price learning — Kleinberg–Leighton 2003 dynamics): shows learning prices against strategic buyers is possible in restricted settings; timing games and multidimensional misreporting are open.

Gap: no framework simultaneously handles (i) multidimensional prices, (ii) intertemporal demand shifting by forward-looking agents, (iii) declared-vs-realized resource usage under execution uncertainty.

## What would count as solving it?

1. **Upper bound (positive result):** A mechanism (multidimensional price update rule, possibly with escrow/slashing on declared usage) and a proof that under a stated strategic-agent model (specify: myopic best response, or discounted-Nash), welfare regret is O(T^{1−c}) and each agent's manipulation gain is o(T) or O(√T). Matching the oblivious bound up to constants is the gold standard.
2. **Lower bound (negative result):** A construction showing any online price rule against farsighted agents suffers Ω(T) regret, or agents extracting Ω(T^{1/2+ε}) manipulation gain — establishing a fundamental separation from the oblivious case.
3. **Intermediate:** Any nontrivial regret bound under a restricted but explicit agent class (e.g., agents with bounded lookahead ℓ, or truthful declarations but strategic timing), or a tight characterization of when the oblivious guarantee survives (e.g., update-rule contraction ⇒ unique stable demand ⇒ performative-style regret bound).

A referee needs: formal game/mechanism, defined regret and manipulation gain, and quantitative bounds with proofs or counterexample constructions.

## How to start

1. **Read Angeris–Diamandis–Moallemi (arXiv:2402.08661) Sections on the price update rule and regret proof** — identify exactly where the proof uses obliviousness of the demand sequence; that's the fault line to attack.
2. **Import performative prediction machinery** (Perdomo et al. 2020; Brown et al. on performative gradient descent): model demand distribution D(p) induced by current prices; if the update rule is a contraction in p and agent best responses stabilize, the oblivious bound plausibly transfers via a stability argument. This is the most natural first theorem (variant 3 above).
3. **Then break it with timing:** model a farsighted agent who knows the deterministic update rule; check whether predictable price movement creates arbitrage (submit when price is about to fall). Try a randomized/decoy update rule to kill exploitability, at some regret cost.
4. **Misreporting:** treat declared-vs-realized usage as an exploration problem; consider escrow + ex-post settlement (charge realized usage, slash discrepancies) and prove truthfulness or bounded gain.

**Main obstacle:** closing the loop — prices shape the demand that prices are learned from — turns convex optimization into a stochastic game; equilibrium existence and learnability of the game are nontrivial.

## Key references

- Angeris, G., Diamandis, P., Moallemi, C. (2024). *Multidimensional Blockchain Fees are (Essentially) Optimal*. arXiv:2402.08661. Proves near-optimal online multidimensional price-setting for blockchain resources under specified demand sequences — the baseline result to extend. https://arxiv.org/abs/2402.08661
- Roughgarden, T. (2021). *Transaction Fee Mechanism Design for the Ethereum Blockchain: An Economic Analysis of EIP-1559*. arXiv:2012.00854. Game-theoretic incentive analysis of EIP-1559 including myopic and some non-myopic user models, single-resource. https://arxiv.org/abs/2012.00854
- Perdomo, J., Zrnic, T., Mendler-Dünner, C., Hardt, M. (2020). *Performative Prediction*. arXiv:2002.06673. Framework for optimization where the model changes the data distribution; source of stability/contraction tools. https://arxiv.org/abs/2002.06673
- Kleinberg, R., Leighton, T. (2003). *The Value of Knowing a Demand Curve: Bounds on Regret for Online Posted-Price Auctions*. FOCS 2003. √T-regret price learning against buyers; the closest classical strategic-online-learning analogue. https://www.cs.cornell.edu/~rdk/papers/postedprices.pdf
- Ferreira, M., Moroz, D., Seshia, S., Parkes, D. (2021). *Dynamic Base Fees in EIP-1559: Pricing Blockchain Resources with Time-Varying Demand*. Analyzes base-fee dynamics and user timing incentives under time-varying demand. (verify link: search title, arXiv ~2109.01338.)

## Warnings

- The census record is a research-target note, not a precise problem statement: agent model, benchmark, and feedback model are all unspecified — you must fix them or a referee will reject the setup.
- Do not assume Angeris et al.'s result covers strategic users; it explicitly does not (specified sequences). Verify the exact assumption in the paper before building on it.
- Timing games often produce no pure-strategy equilibrium; you may need to work with ε-equilibria or undiscounted-average play, and regret bounds may hold only per-realization vs. ex-ante.
- Naively importing performative prediction assumes a *fixed* distribution map D(p); strategic timing makes D depend on the *entire future price trajectory*, breaking contraction arguments — this is the likely dead end.
- Distinguishing "manipulation gain" from legitimate intertemporal substitution of demand matters: bound only the welfare-relevant distortion, or the target becomes unachievable or trivial.
- Last reference (Ferreira et al.) identifier marked (verify); confirm before citing.

---
*`BCGT-000287` · flagged for review*

## Acceptance guidance

1. **Upper bound (positive result):** A mechanism (multidimensional price update rule, possibly with escrow/slashing on declared usage) and a proof that under a stated strategic-agent model (specify: myopic best response, or discounted-Nash), welfare regret is O(T^{1−c}) and each agent's manipulation gain is o(T) or O(√T). Matching the oblivious bound up to constants is the gold standard.
2. **Lower bound (negative result):** A construction showing any online price rule against farsighted agents suffers Ω(T) regret, or agents extracting Ω(T^{1/2+ε}) manipulation gain — establishing a fundamental separation from the oblivious case.
3. **Intermediate:** Any nontrivial regret bound under a restricted but explicit agent class (e.g., agents with bounded lookahead ℓ, or truthful declarations but strategic timing), or a tight characterization of when the oblivious guarantee survives (e.g., update-rule contraction ⇒ unique stable demand ⇒ performative-style regret bound).

A referee needs: formal game/mechanism, defined regret and manipulation gain, and quantitative bounds with proofs or counterexample constructions.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/a6de8e61-3cc5-46c8-b33f-4649a42f6a8a?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000287.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000287)
