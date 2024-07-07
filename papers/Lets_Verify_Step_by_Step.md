# Paper

- Title: Let's Verify Step by Step
- Authors: Hunter Lightman, Vineet Kosaraju, Yura Burda, Harri Edwards, Bowen Baker, Teddy Lee, Jan Leike, John Schulman, Ilya Sutskever, Karl Cobbe
- Link: https://arxiv.org/abs/2305.20050
- Repository: https://github.com/openai/prm800k
- Tags: reasoning, math word problems
- Year: 2023

# Summary

- What
  
  - They introduce a framework for process supervision, providing to the model feedback for each intermediate step of the reasoning process (as opposed to providing feedback just for the final result).
  - They release PRM800K: a dataset of 800K step-level human feedback labels.

<img src="https://github.com/lisaalaz/papers/blob/master/images/Lets_verify_overview.png" width="800">



- How

  - Method

    - They train a reward model to discriminate between "desirable" and "undesirable" model outputs.
    - Note that this reward model can be used either in a RL pipeline or to sample multiple outputs and discard the low-reward ones.
    - This work only focusses on training a reliable reward model, and does not apply this to the model generating the answers (i.e. the "generator").
    - The reward model predicts the correctness of each step as a single token after the token of that step. It is trained to predict the probability of positive, negative, and neutral tokens.
  
  
  - Dataset
    
  <img src="https://github.com/lisaalaz/papers/blob/master/images/Lets_verify_data_collection.png" width="800">



    - Human annotators score each step of model-produced solutions to the MATH dataset as either "positive", "neutral" or "negative" (where  a neutral label indicates ambiguity. At test time neutral labels can be treated as either positive or negative).
    - They collect the 800K step-level labels into a dataset (PRM800K). These span 75K solutions to 12K MATH problems. Note that these include problems from the MATH test set (so they only evaluate on the remaining test samples not in this dataset).
    - Note that they select to show to the human annotators solutions that are rated highly by a current step-level reward model (note they continue to retrain this throughout the process on the new data they receive), but that reach the wrong final solution. In this way they ensure the annotators scores are most useful.
    - They use this dataset to train a large scale reward model.
  


- Evaluation

  - They evaluate their step-level, or process-supervised, reward model (PRM) against an outcome-supervised reward model (ORM) that only looks at the final solution.
  -  To evaluate the goodness of a reward model, they (uniformly) sample N solutions from the generator, select the one ranked highest by the reward model, and check if the answer is correct. The larger the proportion of highest-ranked solutions that are also correct, the better the reward model.
  -  To obtain a single score for the entire solution from the PRM they compute the product of the correctness probabilities of each step.


- Results

  <img src="https://github.com/lisaalaz/papers/blob/master/images/Lets_verify_results_1.png" width="800">

  <img src="https://github.com/lisaalaz/papers/blob/master/images/Lets_verify_results_2.png" width="800">

  <img src="https://github.com/lisaalaz/papers/blob/master/images/Lets_verify_results_3.png" width="800">
