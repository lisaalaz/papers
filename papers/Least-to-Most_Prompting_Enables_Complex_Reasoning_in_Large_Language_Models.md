# Paper

- Title: Least-to-Most Prompting Enables Complex Reasoning in Large Language Models
- Authors: Denny Zhou, Nathanael Schärli, Le Hou, Jason Wei, Nathan Scales, Xuezhi Wang, Dale Schuurmans, Claire Cui, Olivier Bousquet, Quoc Le, Ed Chi
- Link: https://arxiv.org/abs/2205.10625
- Repository: unavailable
- Tags: Reasoning, prompting, chain-of-thought
- Year: 2023

# Summary

- What
  
  - They propose a novel prompting strategy called least-to-most prompting.


- How

  - Strategy

    Least-to-most prompting includes two stages:
    1) First, a problem is decomposed into a list of easier subproblems,
    2) Then, the subproblems are solved sequentially, each subproblem being facilitated by the answers to previously solved subproblems.

    Both stages are  implemented using few-shot prompting, without any training or finetuning involved.

    ![image](https://github.com/lisaalaz/papers/assets/89645136/bd928293-6b79-4c8b-91e1-76c6a54ec49d)
  
  - Architecture
    
    - To decompose a problem into a list of easier subproblems, a LLM is used. The prompt to this LLM contains a number of examples that demontrate decomposition, followed by the specific question to be decomposed. The LLM thus outputs all intermediate smaller subproblems that need to be solved to solve the overall complex problem.
    - To solve each subproblem, a three-part strategy is followed:
      1) First, the prompt contains a number of examples that demostrates how subproblems are solved. 
      2) Then, a list of previously answered subquestions are added to the prompt too. This list can be empty if we are solving the first subproblem.
      3) Finally, the next problem/question to be answered is added to the prompt.
    - For all experiments, they use GPT-3 `code-davinci-002` as the base model to prompt.

- Results

  - Last-letter-concatenation:
    - Last-letter-concatenation is a symbolic manipulation task where each input is a list of words, and the corresponding output is the concatenation of the last letters of the words in the list (e.g., “thinking, machine” -> “ge”).
    - Chain-of-thought prompting performs well on this task when the test examples have the same number of words as the exemplars, but poorly when they are much longer.
    - Least-to-most prompting, on the other hand, decomposes a list of words into a sequence of sublists.
    
    <img src="https://github.com/lisaalaz/papers/blob/master/images/L2M_table1_2.png?raw=true" width="800">


    - By doing this, it achieves a much higher accuracy than chain-of-thought, with the difference in performance increasing as the number of words in the test examples increases. Both prompting methods are implemented using `code-davinci-002`.

    <img src="https://github.com/lisaalaz/papers/blob/master/images/L2M_table4.png?raw=true" width="800">

  - SCAN:
    - SCAN is a popular benchmark for evaluating compositional generalization. The purpose of this task is mapping natural language commands to sequences of actions. S2S models perform poorly on this task when the dataset is split so that the train examples are shorter than the test examples, and for this reason this has traditionally been a task solved by trained neuro-symbolic models.
    
    <img src="https://github.com/lisaalaz/papers/blob/master/images/L2M_table5.png?raw=true" width="800">
    
    - On the other hand, LLMs with least-to-most prompting require no training or finetuning to solve this task.
    - In the least-to-most setting, a first prompt shows how to decompose a long command into a series of short commands by providing eight exemplars, then a second prompt uses 14 exemplars to show how to map the natural language commands to action sequences.
  
    <img src="https://github.com/lisaalaz/papers/blob/master/images/L2M_table6.png?raw=true" width="800">
    <img src="https://github.com/lisaalaz/papers/blob/master/images/L2M_table7.png?raw=true" width="800">

    - Here chain-of-thought achieves an accuracy of 16.2 with `code-davinci-002` but zero using other models (`text-davinci-002` and `code-davinci-001`), whereas least-to-most achieves 99.7%, 76% and 60.7% on the three models respectively.

    <img src="https://github.com/lisaalaz/papers/blob/master/images/L2M_table8.png?raw=true" width="800">

  - GSM8K and DROP
    - These two datasets contain maths word problems.
    
    <img src="https://github.com/lisaalaz/papers/blob/master/images/L2M_table9_10.png?raw=true" width="800">
    
    - The least-to-most passed for GSM8K consists of two parts: the first part shows how the original problem can be decomposed into simpler subproblems, while th second part shows how the subproblems are solved in sequence. This prompt thus combines decomposition and subproblem solving into one single instruction.
    - While least-to-most prompting only improves the overall accuracy on GSM8K by a small amount compared to chain-of-thought, this difference is much bigger when we only consider problems which need at least 5 steps to be solved.
    - On DROP, the accuracy difference between chain-of-thought and least-to-most is much greater.
    
    <img src="https://github.com/lisaalaz/papers/blob/master/images/L2M_table11.png?raw=true" width="800">
    <img src="https://github.com/lisaalaz/papers/blob/master/images/L2M_table12.png?raw=true" width="800">
  
