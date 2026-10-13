# The 100 Prisoners Problem with One Allowed Failure

[日本語 / Japanese](README.ja.md) · [Reproduction guide](docs/reproduction.md) · [Round 7 proof and verification](docs/round7-verification.en.md)

Documentation updated **2026-10-13**; research record through **2026-10-12, Round 8**, with the four saved Round 8 construction audits rerun on **2026-10-13**. This repository records a research relay involving ChatGPT, Claude, Codex, and Perplexity. It separates mathematical proofs, explicit constructions checked by exhaustive computation, restricted searches, and unresolved optimality questions.

**Allowing one failure makes it possible to strictly improve on ordinary cycle following.** A general construction guarantees an improvement of at least $1/495000$ in the 100-person problem. For eight prisoners, the best saved construction rescues **52 directed 5-cycles and three directed 7-cycles**, wins **15,051 of 40,320 placements** (about **37.328869%**), and preserves every placement won by cycle following. The number 52 is optimal for the stated 5-cycle rescue objective within a restricted strategy family. **The optimal total winning probability over all strategies remains unknown.**

## Problem

- There are $n=2m$ prisoners and $2m$ numbered boxes. A uniformly random permutation places each number $1,\ldots,2m$ in exactly one box.
- Prisoner $i$ may open at most $m$ boxes and succeeds only by **actually seeing their own number**. Prisoners may agree on a strategy beforehand, but cannot communicate or change the contents after the procedure starts. Each next box may depend on that prisoner's observed history.
- The group wins when **at least $2m-1$ prisoners succeed**. This differs from the classical objective requiring everyone to succeed.

In **cycle following (CF)**, prisoner $i$ first opens box $i$, then repeatedly opens the box numbered by the number just observed. A prisoner succeeds exactly when their permutation cycle has length at most $m$. If a longer cycle exists, at least $m+1$ prisoners fail. Consequently, allowing one failure does not change CF's winning set, and its exact probability is

$$
P_{\mathrm{CF}}=1-\sum_{k=m+1}^{2m}\frac1k.
$$

For 100 prisoners opening at most 50 boxes each, this is $0.3118278206898048\ldots$, approximately **31.182782%**. The often quoted **30.6853%** is the limiting value as the number of prisoners grows, rather than the finite 100-person value.

## General constructions

### One fixed cycle

For every $m\ge2$, fix one directed cycle of length $m+1$. Conditional changes to the final box of prisoners on that cycle preserve all CF wins and add exactly $(m-1)!$ winning placements. The improvement is

$$
\Delta P=\frac{(m-1)!}{(2m)!}.
$$

For $m=50$, this is $49!/100!\approx6.52\times10^{-96}$. This establishes strict improvement, although the gain is extremely small. See the [archived proof](<results/1巡回追跡法を厳密に超える戦略/Claude版/巡回追跡を超える戦略_整形済み.md>).

### Safe cycle packing: Theorem M

Several exceptional cycles can coexist when their associated short loss cycles do not collide. With a common anchor, this becomes a single-deletion condition on words of distinct labels: two selected words must never produce the same word after deleting one symbol.

Theorem M gives an explicit construction for every $m\ge2$. Label the $2m-1$ non-anchor prisoners by $0,\ldots,2m-2$ and partition their length-$m$ words by the pair of checksums

$$
\left(\sum_{t=1}^{m}p_t\bmod(2m-1),\quad
\sum_{t=1}^{m-1}t\,[p_t>p_{t+1}]\bmod m\right).
$$

Each class satisfies the required deletion condition; choosing a largest class guarantees

$$
P_{\mathrm{new}}-P_{\mathrm{CF}}\ge\frac{1}{2m^2(2m-1)}.
$$

Thus the 100-person problem has an attainable winning probability of at least

$$
P_{\mathrm{CF}}+\frac1{495000}
\approx0.311829840892.
$$

This is an **attainable lower bound**, not the optimal probability. Its basis is a general proof, with finite examples used as implementation checks; the 100-person placements were not exhaustively enumerated. See the [proof audit](<results/4ラウンド同じ番号集合で異なる巡回順も安全に詰め込めるか/Cluade/安全な巡回詰め込み構成の証明監査.txt>) and [Round 4 audit log](log/log8.txt). The audit requires an exceptional target to be unopened and different from CF's ordinary next box. The construction satisfies these conditions. Its novelty in the literature has not been established.

### An anchor strategy that ignores observation order

For $m\ge3$, choose $m$ anchors $A$ and a directed cycle $T$ on them. Write $p(a)$ for the predecessor of anchor $a$ in $T$. Non-anchors follow CF. An anchor $a$ makes at most one jump: after observing every number in $A\setminus\{a,p(a)\}$, while not yet having observed $p(a)$ or found their own number, they next open box $p(a)$ if a box opening remains. Afterwards they follow the observed numbers. When this would revisit an opened box, any unopened box may be chosen.

Let $W$ be CF's winning set. Let $R_T$ consist of placements containing a cycle obtained by inserting one non-anchor into one edge of $T$. The set-based strategy $S_T^{\mathrm{set}}$ satisfies

$$
W(S_T^{\mathrm{set}})=W\sqcup R_T,
\qquad |R_T|=m\,m!.
$$

This is a construction theorem for all $m\ge3$, not an optimality theorem. An anchor harmed on an old winning placement must have its prescribed predecessor outside its own cycle; two anchors cannot suffer such a loss simultaneously. Conversely, rescuing all anchors on a long cycle forces its anchor order to be $T$.

The [general proof](repro/S_T_set_proof_2026_10_09.md) is archived in Japanese. The earlier ordered strategy $S_T$ has the same **group winning set**, but individual successes and box-opening paths need not agree. Historical exhaustive checks gave $294/720$ for six prisoners and $14832/40320$ for eight, with zero lost CF wins. Those set-strategy checks are separate from the current Round 7 verification suite.

## Small-instance results and evidence

The table combines historical results, the Round 7 certificate checks, and the four Round 8 constructions rechecked on 2026-10-13. Counts refer to uniformly random permutations, and evidence is stated at its actual scope.

| Prisoners / boxes per prisoner | CF wins | Constructions | Status |
| --- | ---: | --- | --- |
| $4/2$ | $10/24$ | $13/24$; Theorem M gives $11/24$ | An upper-bound proof and audit discussion for $13/24$ are archived. The entire proof was not freshly audited for this release. |
| $6/3$ | $276/720$ | Theorem M: $284/720$; larger anchored design: $288/720$; ordered and set-based anchor strategies: $294/720$ | Historical exhaustive checks over 720 placements. **Optimality of 294 is open.** |
| $8/4$ | $14736/40320$ | Theorem M: $14916/40320$; set-based anchor strategy: $14832/40320$; fixed-anchor 49 cycles: $15030/40320$; free-omission 51 cycles: $15042/40320$; 52 cycles: $15048/40320$; six-cycle coexistence: $15050/40320$; **three-star construction: $15051/40320$** | 49 is optimal in the fixed-anchor packing family. **52 is optimal for 5-cycle rescue by CF-preserving final-box modifications. Total-win optimality is open.** |
| $10/5$ | $1285920/3628800$ | Theorem M: $1293984/3628800$; set-based anchor strategy: $1286520/3628800$ | Theorem M's count was exhaustively checked in the earlier audit; it was not rerun for this release. The set-strategy count follows from the general theorem. Optimality is open. |

The archived [four-person upper-bound document](<results/1巡回追跡法を厳密に超える戦略/Claude版/4人囚人問題_上界13-24の証明.docx>) and [six-person strategy code](<results/1巡回追跡法を厳密に超える戦略/Claude版/verify_294.py>) provide the original evidence. Finite enumeration checks a particular implementation; it does not replace a theorem for arbitrary $m$.

## Rounds 4–8: what changed

### Round 4: 49 with a fixed anchor, 51 with a free omitted prisoner

The fixed-anchor packing uses length-4 words on seven labels. For each ordered pair of labels there are 15 length-3 words containing it, and each selected length-4 word containing the pair consumes two of them. At most seven selected words contain any given ordered pair. There are 42 ordered pairs and each selected word contains six, so $6N\le42\cdot7$, giving **$N\le49$**. A saved 49-cycle construction attains this bound.

A different family allows the omitted prisoner to vary by cycle. Its historical 51-cycle certificate wins 15,042 placements. Round 7 closes the former 51-versus-52 gap for the 5-cycle objective; it does not turn 49 or 52 into an upper bound over all adaptive strategies. The [Round 4 reproduction suite](repro/round4/round4_cycle_packing/run_checks.py) and the saved baseline [rule table](repro/round4_baseline_n8.json) remain available.

### Round 6: restricted mixed-strategy searches

Starting from the 51-cycle baseline, the saved best strategy actually using an early jump wins **15,030** placements. A different final-box strategy rescues two length-6-cycle placements and wins **15,038**, trading away six length-5-cycle placements. Both preserve all CF wins. These are restricted search results, not optimality bounds; neither exceeded the 15,042 baseline. See the [Round 6 report](repro/round6_mixed_search_report.md), [early-jump certificate](repro/best_explored_mixed_n8.json), and [six-cycle certificate](repro/six_cycle_probe_strategy.json).

### Round 7: 52-cycle construction and structural explanation

Three saved constructions each use **208 final-box rules**, rescue **52 directed 5-cycles**, and win

$$
14736+52\cdot3!=15048
$$

of the 40,320 placements, with **zero lost CF wins**. Two implementations directly simulated every placement for each construction in the 2026-10-12 Round 7 checks.

The short upper-bound proof counts directed loss 4-cycles. CF preservation permits at most one side from each of the 35 pairs of complementary four-person supports. Each selected support has six directed cycle words, giving at most 210 available words. Every rescued 5-cycle needs four distinct words, and one rule can correctly rescue only one directed 5-cycle. Therefore

$$
4N_5\le210,\qquad N_5\le52.
$$

The certificates attain this bound. The symmetric construction uses the 35 four-person subsets of $\{1,\ldots,7\}$ and is invariant under $g=(1\ 2\ 3\ 4)(5\ 6)$. It has **13 orbits**, using 208 of 210 words and leaving exactly $(1\ 2\ 3\ 4)$ and $(1\ 4\ 3\ 2)$ unused. Fixing the support family turns the problem into a matching problem in a 4-uniform hypergraph. The [English supplement](docs/round7-verification.en.md) gives the complete scoped counting proof, the 13 representatives, and the rule semantics.

The earlier 51-cycle solution cannot be extended by adding one cycle while retaining its cycles, even if their omitted prisoners are reassigned: the supplied exact extension program rejected all **2,265 candidates** on rerun. A 52-cycle solution nevertheless exists with the same support family after relabeling. The 51-cycle solution is maximal under that extension operation, rather than maximum.

The archived Round 7 searches starting from 52 cycles did not improve 15,048. Keeping the 208 rules fixed and adding rules only on the two unused words was fully scanned in **867 local cases** across the three constructions, also without improvement. These statements are limited to the inspected search spaces. The [independent audit](repro/round7_52_independent_audit.md) records their exact scope.

### Round 8: mixed cycle lengths and 15,051 wins

The scope remains **eight prisoners, four openings each, at least seven successes, the first three boxes following CF, and only the fourth box changed**, with all 14,736 original CF wins preserved. Let $R_k$ count rescued directed cycles of length $k$, rather than individual placements. In this family, $R_8=0$ and

$$
N_{\mathrm{win}}=14736+6R_5+2R_6+R_7.
$$

The 5-cycle upper bound of 52 remains valid, while rescuing additional longer cycles improves the total winning count:

| Construction | Rules | $R_5$ | $R_6$ | $R_7$ | Wins / 40,320 | Lost CF wins |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| One 7-cycle star | 208 | 52 | 0 | 1 | 15,049 | 0 |
| Two 7-cycle stars | 208 | 52 | 0 | 2 | 15,050 | 0 |
| One 6-cycle alongside 52 rescued 5-cycles | 210 | 52 | 1 | 0 | 15,050 | 0 |
| **Three 7-cycle stars** | **208** | **52** | **0** | **3** | **15,051** | **0** |

Each star supplies all seven correct final-box windows for its directed 7-cycle. The best saved construction therefore wins **37.328869%** of placements. On **2026-10-13**, the archived independent auditor was rerun: all four constructions passed direct simulation of all 40,320 placements, structural and CSV certificate checks, and package hash checks. The two separately saved JSON files matched the archive. **15,051 is an attainable count; total-win optimality remains unproved.**

The [three-star certificate](<results/8ラウンド5・6・7巡回の共存条件と競合不等式/Claude/2/best15051_three_stars_n8.json>), [six-cycle certificate](<results/8ラウンド5・6・7巡回の共存条件と競合不等式/Claude/2/best15050_six_cycle_n8.json>), [complete four-construction archive](<results/8ラウンド5・6・7巡回の共存条件と競合不等式/Claude/2/round8_claude_2026-10-12.zip>), and [independent audit report](<results/8ラウンド5・6・7巡回の共存条件と競合不等式/Codex/round8_independent_audit.txt>) specify the strategies and their checks. See [log13](log/log13.txt) for the research record.

**Proofs and local enumeration.** Within this family, $R_5=52$ implies $R_6\le1$. The saved audit independently enumerated 255,879 local rule tables and checked the uniqueness argument behind this bound. If $M$ is the number of rules, $F_5$ counts rescued 5-cycles having all five correct rules, and $E$ counts rules outside rescued 5-cycles, the additional coexistence inequalities are

$$
M=4R_5+F_5+E\le210,\qquad 2R_6\le3E,\qquad R_6\le\frac{E(E-1)}2.
$$

In particular, $R_5=52$ and $R_6\ge1$ force $E=2$, $F_5=0$, $M=210$, and $R_6=1$. For 7-cycles receiving at least six correct windows from 5-cycle blocks, the local classification has **28 partial-star patterns and one full-star pattern**, checked over $6^7=279936$ assignments. These are local block patterns, not 29 different rescued cycles; when extra rules supply windows, their contributions must be retained.

**Completed finite analysis of coexistence.** Under $R_5=52,R_6=1$, every rescued 7-cycle avoids the two external rules, contains the six labels of the rescued 6-cycle, and uses the same seventh label. Exchanging labels 7 and 8 reduces the local candidate list from 936 to **468**, without fixing the support family of the complete rule table. The resulting 7,638 local witnesses give **60,481 pairwise conflict inequalities**. Exact clique enumeration gives a maximum of 11, hence the conditional bound $R_7\le11$. A clique records pairwise local compatibility; it does **not** construct a strategy rescuing 11 cycles or complete a common 52-block design. The saved independent audit reproduced the candidate sets, graph, and bound; [log14](log/log14.txt) records that audit.

A separate [order-5 symmetric certificate](<results/8人囚人問題の混合長巡回救済/Astra/order5_symmetric_210rules_n8.json>) uses 210 rules and wins 15,048 placements with no CF loss. It disproves an unconditional extension of the 208-rule “symmetry group is a 2-group” conclusion to 210 rules. For the specific **order-5 invariant, $R_5=52$, at-most-210-rule branch**, the saved finite analysis and independent audit completed **6,432 branches with zero `UNKNOWN`**, giving $R_7=0$. This conclusion does not exclude asymmetric strategies. The [research report](<results/8人囚人問題の混合長巡回救済/Astra/round8_research_report_ja.txt>) and [machine-readable summary](<results/8人囚人問題の混合長巡回救済/Astra/research_summary.json>) explain these scoped results.

**Unresolved searches.** The six saved general CP-SAT runs ended `UNKNOWN`. The two immediate routes to 15,052 wins remain open: $R_5=52,R_6=1,R_7\ge2$ and $R_5=52,R_6=0,R_7\ge4$. Trades with $R_5\le51$ are also unresolved. `INFEASIBLE` results for full-star-only or selected symmetry models do not settle these general questions. The local enumeration, clique and symmetry branch results above are archived Round 8 results; this README update reran the four saved construction audits, not the CP-SAT searches or all finite branch analyses. The eight-person results do not change the general 100-person lower bound or establish optimality for the 99-success objective.

## Reproduce the saved constructions

From the repository root, run Python 3 with its standard library:

```powershell
python -X utf8 repro/run_verification.py
```

The **2026-10-12 Round 7 release** passed all **13 tasks**. The runner checks the Round 4 suite, independently audits the three Round 7 certificates, simulates each 52-cycle construction with two implementations, checks selected Round 6/7 strategies, scans the 867 local cases, and reruns the 51-cycle extension test. See [current check results](verification/current_checks.json) and the [reproduction guide](docs/reproduction.md). It verifies saved constructions; it does not rerun the heuristic or CP-SAT searches.

The three primary Round 7 strategies are [symmetric](repro/best52_costar_Z4_n8.json), [split unused words](repro/best52_costar_splitY_n8.json), and [weighted threshold family](repro/best52_threshold_n8.json). A JSON rule `[i, history, target]` means that prisoner `i`, after seeing that exact ordered history, opens box `target` next. Their `mixed-n8-v1` schema also permits earlier jumps; **these three certificates contain no early rules**, only 208 histories of length three for the fourth box. The English supplement specifies precedence, stopping, and fallback behavior.

The **2026-10-13 Round 8 recheck** is separate from that 13-task Round 7 runner. From the repository root, audit the four archived Round 8 constructions with Python's standard library:

```powershell
python -X utf8 "results/8ラウンド5・6・7巡回の共存条件と競合不等式/Codex/audit_round8_independent.py" "results/8ラウンド5・6・7巡回の共存条件と競合不等式/Claude/2/round8_claude_2026-10-12.zip" --output round8_certificate_audit.json
```

The [auditor](<results/8ラウンド5・6・7巡回の共存条件と競合不等式/Codex/audit_round8_independent.py>) reads certificate data from the ZIP without executing its scripts. It checks package hashes, raw rule tables, CSV/cycle certificates, and all 40,320 placements for each construction. The resulting JSON should report `status: PASS`, counts 15,049 / 15,050 / 15,050 / 15,051, and zero lost CF wins. It does not rerun the clique, order-5 branch enumeration, or CP-SAT searches.

## Open questions and limits

- The best total winning count for eight prisoners, including final-box strategies that also rescue length-6 or length-7 cycles, is unknown. **15,051 is the best saved attainable count, not a proved total-win optimum.** The immediate Round 8 targets are $R_5=52,R_6=1,R_7\ge2$ and $R_5=52,R_6=0,R_7\ge4$; trades with $R_5\le51$ remain open.
- The best unrestricted adaptive strategy for six prisoners, or for 100 prisoners with one allowed failure, is unknown. The six-person 294 strategy should not be treated as a general optimal strategy.
- For CF-preserving final-box modifications, the number of rescued directed $(m+1)$-cycles obeys $N_{m+1}\le\lfloor(2m-1)!/(m\,m!)\rfloor$. Their probability contribution is at most $1/(2m^3)$. This bound concerns that cycle-length objective; it is not a bound on the total gain of broader strategies.
- Attainment of the general cycle-count bound for $m\ge5$, classification of support families admitting 52 cycles at $m=4$, and safe combinations of multiple early jumps remain open. The Round 7 source contains larger-instance search observations; they were not independently rechecked for this release.
- Novelty in the literature has not been established. Classical all-success optimality results do not establish optimality for this one-failure objective.

## Repository and reading order

Related literature includes [Curtin and Warshauer, *The Locker Puzzle*](https://www.cl.cam.ac.uk/~gw104/Locker_Puzzle.pdf), which treats the classical all-success objective, and [Lodato, Shekatkar, and Wong, *On partial information retrieval: the unconstrained 100 prisoner problem*](https://arxiv.org/abs/2012.13484), which studies success thresholds. These references provide context; they are not cited as proofs of optimality or novelty for the constructions in this repository.

`repro/` contains runnable code and machine-readable certificates; `verification/` contains the current transcripts and results. `results/` preserves source reports and historical artifacts, many in Japanese. `log/` preserves the research conversations. The English README, proof supplement, and reproduction guide provide an entry point without requiring translations of every conversation.

| Record | Subject |
| --- | --- |
| [log1](log/log1.txt) | From the classical puzzle to the one-failure variant; includes claims corrected later. |
| [log2](log/log2.txt), [log3](log/log3.txt) | Four-person $13/24$ construction, upper bound, and audit. |
| [log4](<log/log4-99人.txt>) | Single-cycle improvement for the 100-person setting. |
| [log5](<log/log5-Perplexity.txt>), [log6](<log/log6-Claude.txt>) | Literature investigation, six-person strategies, and general constructions. |
| [log7](<log/log7違い.txt>) | Different strategy mechanisms and losses from unsafe combinations. |
| [log8](log/log8.txt) | Round 4 packing, Theorem M, and proof corrections. |
| [log9](log/log9.txt) | Analysis of the 294 strategy and the ordered anchor theorem. |
| [log10](log/log10.txt) | Set-based anchor conjecture and mixed-strategy research direction; the later general proof is linked above. |
| [log11](log/log11.txt) | Round 6 mixed-strategy exploration for eight prisoners. |
| [log12](log/log12.txt) | Round 7: 52 cycles, structural analysis, independent audit, and the initial Round 8 research plan. |
| [log13](log/log13.txt) | Round 8: 15,049–15,051 constructions, six-cycle coexistence, partial stars, and independent audit. |
| [log14](log/log14.txt) | Round 8: free-support reduction to 468 candidates, conditional $R_7\le11$, order-5 symmetry branch, and independent audit; general searches remain `UNKNOWN`. |

Statements such as “optimal,” “upper bound,” or “unproved” in historical conversations describe their state at the time. Use the scoped conclusions above and the current verification records when citing the repository's present results.
