# Paper

- Title: Tree of Thoughts: Deliberate Problem Solving with Large Language Models
- Authors: Shunyu Yao, Dian Yu, Jeffrey Zhao, Izhak Shafran, Thomas L. Griffiths, Yuan Cao, Karthik Narasimhan
- Link: https://arxiv.org/abs/2305.10601
- Repository: : https://github.com/ysymyth/tree-of-thought-llm
- Tags: Reasoning, prompting, chain-of-thought
- Year: 2023

# Summary

- What
  
  - They introduce a new framework for LM inference called Tree of Thoughts (ToT), which is an alternative to the autoregressive, left-to-right decoding mechanism (the latter, in their assessment, is insufficient for an LM to be a general problem solver).
  - They also propose three novel tasks: Game of 24, Creative Writing and Crosswords. They show that ToT performs better than the largest LLMs on these tasks.

  [insert figure 1 - schematic]

- How

  - Background
    - They cite literature on human cognition identifying a dual process of decision-making comprised of a fast, automatic, unconscious mode (“System 1”) and a slow, deliberate, conscious mode (“System 2”).
    - They cite research on reinforcement learning in humans and other animals that has explored the circumstances under which they engage in associative “model free” learning or more deliberative “model based” planning.
    - They claim that the token-level choices of LM decoding are reminiscent of "System 1" but could benefit from a more deliberate planning process as in "System 2" that explores and evaluates different options before making a decision.

  - Strategy
    - To design the aforementioned planning process for LMs, the authors return to the origins of AI and cognitive science drawing inspiration from planning processes explored by scholars in the 1950s: problem solving was then characterised as a search through a combinatorial problem space represented as a tree.
    - Therefore, their ToT method actively maintains a tree of thoughts where each thought is a coherent language sequences that serves as an intermediate step toward solving a problem.
    - The LM can then self-evaluate the progress that different intermediate thoughts make toward solving the problen.
    - This is combined with search algorithms, e.g. breadth-first search (BFS) and depth-first search (DFS), allowing exploration of the tree, lookahead and backtracking.
  
  - Architecture
    - ToT frames a problem as a search over a tree where each node is a state $s = [x, z_{1...i}]$ representing a partial solution with the input and the sequence of thoughts so far.
    - To instantiate ToT we need:
      1) __Thought decomposition__ to break down the intermediate process of solving a problem into thought steps. The steps are dependent on the problem (e.g. a couple of words for Crosswords, a line of equation for Game of 24, or a whole paragraph of writing plan for Creative Writing). Generalluy speaking, a thought must be small enough that LMs can generate quality and diversified samples (e.g. generating a whole book is usually too “big” to be coherent), yet big enough that an LM can evaluate its prospect toward problem solving (e.g. generating one token is usually too “small” to evaluate).
      2) A __thought generator__ to generate potential thoughts from each state. The generator is a function $G(p_\theta, s, k)$ which given a state $s$ can generate $k$ candidates for the next thought steps in two ways:
         - Sampling i.i.d. thoughts from a chain-of-thought prompt $z^(j) ∼ p_\theta^{CoT}(z_{i+1}|s) = p_\theta^{CoT}(z_{i+1}|x, z_{1...i})(j = 1 ... k)$. This works well when the thought space is rich (e.g. each thought is a paragraph) and i.i.d. samples lead to diversity.
        [add creative writing figure 4 here]
         - Proposing thoughts sequentially using a 'propose prompt' $[z^(1), ..., z^(k)] ∼ p_\theta^{propose}(z_{i+1}^(1...k)|s)$ This works better when the thought space is more constrained (e.g. each thought is just a word or a line), so proposing different thoughts in the same context avoids duplication.
        [add game of 24 figure 2 and crosswords figure 6 here]
      3) A __state evaluator__ to evaluate these states heuristically. The state evaluator $V(p_\theta, S)$ evaluates, given different states, the progress they make toward solving the problem, thus serving as a heuristic for the search algorithm to determine which states to keep exploring and in which order. To this end, the LM is used to reason about states (which requires no training or programming). Again, here two strategies for evaluation are considered (to be used independently or both togeher):
         - Valuing each state independently, where the value $V$ is given by $V(p_\theta, S) ∼ p_\theta^{value}(v|s)\any s \in S$. Here a value prompt reasons about the state $s$ to generate a scalar value $v$ or a classification label that can heuristically be converted into a value. The reasoning behind this evaluation varies according to the task. They explore evaluation via lookahead simulations (e.g. quickly confirm that 5, 5, 14 can reach 24 via 5 + 5 + 14, or “hot_l” can mean “inn” via filling “e” in “_”) plus commonsense (e.g. 1 2 3 are too small to reach 24, or no word can start with “tzxc”). The former aims at promoting good states, whereas the latter at filtering out bad” states.
         - Voting across states. Here we have $V(p_\theta, S)(s) = \ones[s=s*]$, where a good state $s* ∼ p_\theta^{vote}(s*|S)$ is voted by comparing different states in S by using a vote prompt. When problem success is harder to directly value (e.g. passage coherency), it is natural to to instead compare different partial solutions and vote for the most promising one.
      4) A __search algorithm__ to search the tree. Different search algorithms can be used in the ToT framework the choice depending on the tree structure. In particular they explore BFS and DFS.
    
  
- Results

  - The three novel tasks they propose (Game of 24, Creative Writing and Crosswords) require deductive, mathematical, commonsense and lexical reasoning abilities, as well as systematic planning or search. These tasks are challenging even for the largest and most capable LLM currently available (i.e. GPT-4).

  - Game of 24 is a mathematical reasoning challenge, where the goal is to use 4 numbers and basic arithmetic operations to obtain the number 24.

  
