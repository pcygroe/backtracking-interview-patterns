# backtracking interview questions: how to spot the pattern, the template that generalizes, and the problems worth drilling

Nobody opens a coding interview with "please write N-Queens." What actually happens is you get a problem about generating every valid combination, or every way to split a string, or every placement that satisfies a constraint — and the interviewer watches to see whether you reach for nested loops, a recursion with the right bookkeeping, or a DP table that doesn't apply here.

That's the real content of backtracking interview questions. Not the code, which is short. The judgment call at the front.

This guide covers what gets asked, the recognition test that tells you backtracking is the right tool, the two templates that cover most problems, and the traps that turn a correct answer into a rejected one.

## What interviewers are testing when they ask a backtracking question

The theoretical part is usually a warm-up, not the main event. Still, it comes up often enough to be worth rehearsing. GeeksforGeeks' collection of commonly asked backtracking interview questions reflects the standard set:

- What is backtracking, and when should you use it?
- How does backtracking differ from brute force?
- How does it differ from dynamic programming?
- What role does recursion play?
- What is a decision tree in this context, and what is pruning?
- Why is backtracking considered exponential, and what are the time and space complexities?

On paper, those look like vocabulary questions. In practice they're a probe for whether you understand *why* your solution is correct. If you say "backtracking prunes invalid paths early," the natural follow-up is "prune based on what, exactly, and does that change your worst-case complexity?" The honest answer for most of these problems is no — pruning improves practical runtime but not the worst case. Saying that out loud reads as competence.

## The recognition test: is this a backtracking problem?

Four signals, in rough order of reliability.

**1. You're asked to return all solutions, not the best one.** "Return all unique combinations" → search. "Return the maximum profit" → DP or greedy. This is the cleanest split between backtracking and dynamic programming, and it holds up: Subsets wants everything, House Robber wants one number.

**2. The answer is a sequence of decisions, and a partial sequence can already be wrong.** Combination Sum is the standard demonstration. For `candidates = [2,3,6,7]`, `target = 7`, brute force generates all 16 subsets and filters. Backtracking stops the moment the running sum exceeds 7. Same worst case, very different practical cost.

**3. The decision tree is what you're actually exploring.** Each node is a state, each branch is a choice, leaves are complete or dead. If you can draw that tree on the whiteboard in under a minute, you've probably found the pattern.

**4. The constraints are small — and this is the tell people ignore.** Look at the actual limits on the problems:

| Problem | Constraint | Why it's small |
| --- | --- | --- |
| Permutations (46) | `1 <= nums.length <= 6` | 720 outputs max, fine to enumerate |
| Subsets (78) | `1 <= nums.length <= 10` | 2¹⁰ = 1,024 subsets |
| N-Queens (51) | `1 <= n <= 9` | search space explodes past that |
| Palindrome Partitioning (131) | `1 <= s.length <= 16` | 2¹⁵ possible splits |
| Word Search (79) | board ≤ 6×6, word length ≤ 15 | depth bounded by word length |

When the input is capped at 16, the problem is telling you the intended complexity is exponential. That's a permission slip, not a warning. Candidates who try to find a polynomial solution to Palindrome Partitioning waste ten minutes of a 45-minute interview.

## The two templates that cover most of it

AlgoMonster teaches backtracking as two variations, and the split is genuinely useful.

**Template 1 — enumerate and report.** Use when you need to collect the actual solutions.

python
def dfs(start_index, path):
    if is_leaf(start_index):
        report(path)
        return
    for edge in get_edges(start_index):
        path.append(edge)          # choose
        dfs(start_index + 1, path) # recurse
        path.pop()                 # undo


Subsets, permutations, combinations, N-Queens, Sudoku all fit this shape. What changes between problems is the three functions: what counts as a leaf, what the legal edges are, and how you validate them.

**Template 2 — aggregate a value back up.** Use when you need "does a valid decomposition exist," a count, or a sum. Word Break is the classic case: you don't want the partitions, you want a boolean.

python
def dfs(start_index):
    if start_index == len(target):
        return True
    ans = False
    for word in words:
        if target[start_index:].startswith(word):
            ans = ans or dfs(start_index + len(word))
    return ans


The difference matters under pressure. Candidates who only have the "collect everything into a list" template write Word Break by generating every partition and then checking if any is valid. It works, it's slower, and it looks like pattern-matching instead of understanding.

If you'd rather see both templates tied to the specific problems, AlgoMonster's backtracking section walks through the choose/recurse/undo structure on real interview problems: 👉 [see how the backtracking pattern is taught step by step](https://algo.monster?fpr=li69).

## The problems that actually get asked

Backtracking is a broad tag — roughly 106 LeetCode problems carry it, and the difficulty distribution skews hard: about 67% medium, 30% hard, 3% easy. You are not going to grind all 106, and you shouldn't try.

A better anchor is LeetCode's Top 100 Liked list, which contains exactly eight backtracking problems: Letter Combinations of a Phone Number (17), Generate Parentheses (22), Combination Sum (39), Permutations (46), N-Queens (51), Subsets (78), Word Search (79), and Palindrome Partitioning (131). That's the core set. Everything below is a variation on those mechanics.

| Problem | Difficulty | What changes from the base template |
| --- | --- | --- |
| Letter Combinations of a Phone Number (17) | Medium | Edges come from a digit-to-letters map; depth = digits.length |
| Generate Parentheses (22) | Medium | Pruning on counts: never more `)` than `(`, never exceed n pairs |
| Combination Sum (39) | Medium | Reuse allowed, so the next index starts at the current one |
| Combination Sum II (40) | Medium | Sort first, then skip adjacent duplicates |
| Permutations (46) | Medium | Needs a `used[]` array or in-place swapping |
| Permutations II (47) | Medium | `used[]` plus a duplicate skip |
| Subsets (78) | Medium | Simplest form: one branch include, one branch exclude |
| Subsets II (90) | Medium | Same, with sorted duplicate skipping |
| Combinations (77) | Medium | Fixed depth k, start index prevents reordering |
| Palindrome Partitioning (131) | Medium | Edges are palindromic prefixes; precompute palindrome checks |
| Restore IP Addresses (93) | Medium | Segments must be 0–255, no leading zeros, exactly four parts |
| Word Search (79) | Medium | Grid backtracking: mark the cell, recurse in 4 directions, unmark |
| Word Search II (212) | Hard | Same, but a trie prunes whole branches that can't form a word |
| N-Queens (51) | Hard | Column + two diagonal sets make validity checks O(1) |
| N-Queens II (52) | Hard | Count solutions instead of building boards |
| Sudoku Solver (37) | Hard | Return as soon as one solution is found, don't collect |
| Expression Add Operators (282) | Hard | Carries the previous operand so `*` can be applied correctly |
| Remove Invalid Parentheses (301) | Hard | Track the minimum removals; dedupe identical states |

The grouping matters more than the list. Fifteen problems across the four families — enumeration, constraint placement, string segmentation, grid search — cover the mechanics. The other ninety are variations you'll recognize once you've internalized the four.

## Traps that cost people the offer

These repeat constantly, and every one of them is avoidable.

**Returning references instead of copies.** `result.append(current)` in Python appends a pointer. When `current` gets mutated on the way back up, every stored result mutates with it. You'll end the run with a list of empty lists. `current[:]` fixes it, and copy cost is part of your complexity analysis — say so.

**Forgetting the undo.** `path.pop()` must mirror the `path.append()` exactly. In grid problems, that means unmarking the visited cell; in Sudoku, resetting the cell to empty. Miss it and you get subtly wrong answers that pass the example test case.

**Getting duplicates wrong.** Subsets II, Permutations II, and Combination Sum II all hinge on the same trick: sort the input, then skip `i > start and nums[i] == nums[i-1]`. If you haven't sorted, the skip logic doesn't work — which surprises people mid-interview.

**Confusing permutations and combinations.** Combinations and subsets carry a `start` index forward so `[1,2]` and `[2,1]` never both appear. Permutations need a `used[]` array (or swapping) because order matters. Mixing these up produces either duplicates or missing answers.

**Checking constraints after the recursion instead of before.** Pruning only works if the check happens before you descend. A Sudoku solver that validates the board at the leaf is technically correct and practically useless.

**Getting the base case half right.** There are two rules, and you need both: a stop when the solution is complete (record and return), and a stop when the path is dead (return without recording). Missing the first means you never record anything. Missing the second means you recurse past valid states.

**Not knowing the complexity.** It's fine to say exponential. It's better to say *how* exponential: subsets are O(n · 2ⁿ), permutations O(n · n!), N-Queens O(n!), letter combinations O(n · 4ⁿ), word search O(m · n · 3^l) since the first cell has four neighbors and every later cell has three. Space is usually O(n) for the recursion depth, plus whatever you're storing as output. A practical technique from the Codepath UMPIRE cheat sheet: count the possible states, estimate the work done per state, multiply.

## Should you buy a course, or just practice?

Backtracking is one of the few patterns where a structured explanation beats volume. The mechanics are learnable in a few hours; the failure mode is recognition, not implementation. Solving the 40th permutation variant won't fix a recognition problem.

AlgoMonster is built around that premise — it's a pattern-first course written by ex-Google engineers, covering the approach behind each problem rather than leaving you to infer it from solution code. Relevant details if you're considering it:

- Backtracking is taught as two named templates (enumerate vs. aggregate), which maps cleanly onto the recognition test above.
- Solutions are published in Python, Java, JavaScript, C++, Go, and Haskell — useful if your interview language isn't Python.
- The format is text and interactive diagrams. There is no video content, which earlier reviews consistently flag as the main limitation for visual learners.
- Speedrun mode adds timed practice, and the AI assistant gives hints when you're stuck — both are unlimited on paid plans.
- Reviews from earlier years quote 48 patterns and 231 problems; the current subscribe page advertises 500+ lessons across 6 courses, 1,000+ illustrations, and 3,000+ editorials. Worth noting that the problem volume is still far smaller than LeetCode's bank, so most candidates pair it with LeetCode for raw practice.

### Current plans and pricing

AlgoMonster doesn't offer monthly billing. It's free access, annual, or lifetime.

| Plan | What you get | Price | Billing | Link |
| --- | --- | --- | --- | --- |
| Free | Limited access to a sample of patterns and problems — enough to judge the format | $0 | — | [Start the free tier](https://algo.monster?fpr=li69) |
| Pro — Yearly | All patterns, lessons and problems; unlimited AI assistant; unlimited Speedrun; company questions updated monthly; 500+ lessons across 6 courses; 1,000+ illustrations; 3,000+ editorials; Foundation and Build an LLM courses included; member discount on live bootcamps | $99/year (displayed as $8.25/month billed annually) | 12 months, renews | [Get Pro Yearly](https://algo.monster?fpr=li69) |
| Pro — Lifetime | Everything in the annual plan plus lifetime updates, billed once | $189 | One-time | [Get Pro Lifetime](https://algo.monster?fpr=li69) |

Two caveats worth checking before you pay. First, AlgoMonster runs frequent sales, and third-party review sites still quote much higher figures — $119–$299/year and $459 lifetime — from earlier pricing. The $99 and $189 above are what the official subscribe page currently lists, but confirm at checkout rather than trusting any page, including this one. Second, coupon and partner sites advertise extra codes on top of the sale price; those change often, so if the checkout has a promotion code field, it costs nothing to try, but don't assume a specific code works.

Verdict on value: $99 for a year is cheaper than most single mock-interview packages, and if your gap is pattern recognition rather than volume, it addresses exactly that. If you already identify patterns reliably and just need reps, this isn't the bottleneck and the money is better spent elsewhere.

## Quick answers to the questions people search for

**Do I need to solve all 106 tagged backtracking problems?** No. The Top 100 Liked list contains eight of them. Fifteen well-chosen problems across the four families will teach you more than a hundred random ones.

**Backtracking or DP?** Ask whether you need all solutions or the optimal value. All solutions → backtracking. Optimal value with overlapping subproblems → DP. Some problems, like Word Break, are genuinely both — backtracking with memoization collapses the exponential search.

**Is recursive backtracking acceptable in interviews, or do they expect iteration?** Recursion is the expected form here. Iterative versions exist via an explicit stack, but nobody asks for one unless they're testing stack mechanics specifically.

**How long does this take to learn properly?** The two templates take an afternoon. Recognizing which one applies under pressure, on a problem you haven't seen, takes a few weeks of deliberate practice — which is precisely the skill the interview is measuring.
