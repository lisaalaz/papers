# Paper

- Title: PromptBench: Towards Evaluating the Robustness of Large Language Models on Adversarial Prompts
- Authors: Kaijie Zhu, Jindong Wang, Jiaheng Zhou, Zichen Wang, Hao Chen, Yidong Wang, Linyi Yang, Wei Ye, Yue Zhang, Neil Zhenqiang Gong, Xing Xie
- Link: https://arxiv.org/abs/2306.04528
- Repository: https://github.com/microsoft/promptbench
- Tags: robustness, prompt perturbation, LLMs
- Year: 2023

# Summary

- What
  
  - They introduce a new benchmark - PromptBench - for measuring LLMs' robustness to adversarial prompt perturbations. In this paper, prompts are defined as the set of instructions telling an LLM how to perform the task, and are separate from the (optional) input sample. The authors note that, in practice, a single instruction prompt is often used with multiple samples when performing tasks. (Note that other works, e.g. AdvGlue and ANLI, focus on LLM robustness to perturbations of the input sample instead.)
  - They analyse the performance of a set of LLMs on 8 tasks (13 datasets in total) given these perturbations of the prompts (and find that the LLMs lack robustness).

<img src="" width="500">

- How

  - Dataset
    
    <img src="" width="500">

    - PromptBench contains 4,788 adversarial prompts in total
    - it consists of 2 different types of prompts:
  
      1) role-oriented (explicitly asking the model to answer as an expert, advisor, etc.)
      2) task-oriented (providing a description of the task, which encourages the model to generate task-specific outputs based solely on its pre-training knowledge)
    
      each of which can be framed either in zero-shot (ZS) or few-shot (FS) fashion.

      <img src="" width="500">

    - For each of these types of prompts, the authors have 4 types of possible attacks:
   
      1) character-level ( adding, deleting, repeating, replacing, and permuting characters for certain words)
      2) word-level (replace words with synonyms or contextually similar words)
      3) sentence-level (append irrelevant or extraneous sentences to the end of prompts)
      4) semantic-level (back-translating from Chinese, French, Arabic, Spanish, Japanese and Korean)
    
      to create the perturbations, the authors extend and use 7 types of adversarial attacks from the literature:

      It should be stressed that PromptBench focusses on adversarial attacks of the *instruction prompt* rather than the input sample. Here the term "adversarial" is used loosely: attacks aim at simulating common errors and perturbation that may naturally occur.
      The prompt perturbations that prove most effective at undermining the model's response, while also remaining acceptable/imperceptible to the human reader, are selected for inclusion into the dataset.

      <img src="" width="500">

    - The dataset spans 8 tasks (and 13 datasets in total pertaining to these tasks):

      1) sentiment analysis (SST-2)
      2) grammar correctness (CoLA)
      3) duplicate sentence detection (QQP, MRPC)
      4) natural language inference (MNLI, QNLI, RTE, WNLI)
      5) multi-task knowledge (MMLU)
      6) reading comprehension (SQuAD V2)
      7) translation (UN Multi, IWSLT 2017)
      8) math problem-solving (Mathematics)
  


- Evaluation

  - They evaluate 9 LLMs (ranging different sizes and both open and closed source). See fig. 2 for list.

  - Since an absolute performance drop may not be best at representing the wide variety of tasks (each with different evaluation metrics and performance), the authors introduce as an evaluation metric the Performance Drop Rate (PDR), representing the *relative* performance drop following a prompt perturbation. This is given by
 
    $PDR(A, P, f_\theta, D) = 1 - \frac{\sum_{(x;y)\in D}{M[f_\theta([A(P),x]), y]}}{\sum_{(x;y)\in D}{M[f_\theta([P,x]),y]}}$

    where $A$ is the adversarial attack applied to prompt $P$, and $M[\cdot]$ is the evaluation function: for classification it is the indicator function $\mathbb{1}[\hat{y}, y]$ which equals to 1 when $\hat{y} = y$, and 0 otherwise; for reading comprehension, it is the F1-score; for translation tasks it is the Bleu score.

    The greater the PDR, the more performance is negatively impacted by the perturbation.


  - They report the average PDR score (APDR) across attacks, models and prompts.


- Results

  <img src="" width="500">

  <img src="" width="500">

  <img src="" width="500">
  
  <img src="" width="500">
