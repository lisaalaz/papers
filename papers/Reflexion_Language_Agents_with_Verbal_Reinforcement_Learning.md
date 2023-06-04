# Paper

- Title: Reflexion: Language Agents with Verbal Reinforcement Learning
- Authors: Noah Shinn, Federico Cassano, Beck Labash, Ashwin Gopinath, Karthik Narasimhan, Shunyu Yao
- Link: https://arxiv.org/abs/2303.11366
- Repository: https://github.com/noahshinn024/reflexion
- Tags: Reasoning, prompting, reinforcement learning, decision making
- Year: 2023

# Summary

- What
  
  - They introduce Reflexion, a verbal reinforcement framework whereby the agent maintains an episodic memory buffer to induce better decision making in subsequent trials.
  - Reflexion is an alternative to prompting LLMs with in-context examples.

- How

  - Strategy
    - Reflexion converts binary or scalar feedback from the environment into verbal feedback (i.e. a textual summary) which is then added to a long term memory store which is added to the LLM prompt in the next episode.
    - This prompt helps the model "reflect" on its previous failures and thus form an improved plan for the next attempt. Hence the model learns by trial-and-error and self-reflection (the authors claim this setup is similar to human learning).
    - This approach is lightweight: no update of the weights is performed.
    - On the other hand, it relies on LLM self-evaluation and is only as good as that capability for self-evaluation is.

    [add reflexion strategy img]
  
  - Architecture

    The Reflexion framework consists of the following elements:
    1) __Actor:__ denoted by $M_a$, is a LLM prompted to generate text and actions conditioned on the state observations. As in traditional reinforcement learning, the action or generation $a_t$ is sampled from the policy $\pi_\theta$ at time $t$ and an observation $o_t$ is received from the environment. A memory component provides additional context to the actor.
    2) __Evaluator:__ denoted by $M_e$, evaluates the outputs produced by $M_a$ by taking a generatd trajectory as input and outputting a reward score. To generate good verbal feedback the following is needed:
       - A good understanding of where the model made mistakes.
       - The the ability to generate a summary containing actionable insights for improvement.
  
        The authors explore three ways of doing this:
       - Simple binary environment feedback such as exact match (EM), used for reasoning tasks.
       - Pre-defined heuristics (these are useful for common failure cases) which they use in decision-making tasks.
       - Self-evaluation using an LLM (this can be binary classification for decision making or self-written unit tests for programming tasks).
    3) __Self-reflection model:__ denoted by $M_{sr}$, is a LLM that generates verbal cues to provide feedback to the Actor. This model takes a reward signal (e.g. a binary success/fail status), the current trajectory and the memory $mem$ and ourputs textual feedback. This feedback is stored in the memory.
    4) __Memory:__ in Reflexion there exist both short and long term memory, both of which aid the decisions of the Actor at inference time. The short term memory in this setting is the trajectory history, whereas the long term memory, denoted by $mem$, stores the textual feedback output by $M_{sr}$.
    

- Results

  They evaluate on several tasks:
  - Sequential decision making: ALFWorld

    AlfWorld is a suite of text-based environments that challenge an agent to solve multi-step tasks in various interactive environments. In this setup, they use ReAct as the Actor, and implement two self-evaluation techniques: natural language classification using an LLM and a
    hand-written heuristic where if the agent receives the same response upon executing the same action for more than 3 cycles, or if the current cycle requires more than 30 actions, then a self-reflection is triggered. The agent's memory is truncated to latest 3 self-reflections. Results are reported after running the agent in 134 AlfWorld environments across six different tasks.

    [insert alfworld results img]

  - Reasoning: HotpotQA

    HotPotQA is a dataset based on Wikipedia containing QA pairs that require parsing content and reasoning over several supporting documents. Again they implement the Actor using ReAct and implement a Wikipedia API to retrieve documents.

    [insert hotpotqa results img]

  - Programming

    They evaluate on Python and Rust code writing on MBPP, HumanEval, and LeetcodeHardGym (the latter is a novel dataset they introduced). They evaluate with unit tests, using Chain-of-Thought prompting to produce tests and sampling a maximum of 6 unit tests from this generated pool. The Actor is again implemented with a ReAct model setting the maximum memory to 1 experience.
  
    [insert programming results img 1 and 2]