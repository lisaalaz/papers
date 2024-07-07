# Paper

- Title: Exploring and Benchmarking the Planning Capabilities of Large Language Models
- Authors: Bernd Bohnet, Azade Nova, Aaron T Parisi, Kevin Swersky, Katayoon Goshvadi, Hanjun Dai, Dale Schuurmans, Noah Fiedel, Hanie Sedghi
- Link: https://arxiv.org/abs/2406.13094
- Repository: N/A
- Tags: planning, reasoning, LLMs
- Year: 2024

# Summary

- What
  
  - They investigate LLMs' planning abilities in a variety of settings: in-context learning (with both few-shot and many-shot prompting), finetuning, both with smaller and larger LLMs and on both in-domain and OOD data.
  - They find that long-context and many-shot prompting performs much better than few-shot, and fine-tuning on optimal planning paths is even better than any ICL framework, especially for smaller LLMs where they obtain near-perfect accuracy. They also show that fine-tuning generalises better on OOD scenarios, even with smaller models. Finally, they find that integrating a search procedure such as Monte Carlo Tree Search (MCTS) helps smaller models perform close to SoTA LLMs.
  - They also introduce a mapping method to translate any problem in Planning Domain Definition Language (PDDL) into natural language.


- Evaluation

  - They evaluate on BlocksWorld, Logistics and Mini-Grid, both expressed in PDDL and in natural language. 
  - They also evaluate on two new natural language planning benchmarks: TripPlanning and Calendar Scheduling, both from the NaturalPlan benchmark.
  - They use Gemini models of various sizes as well as GPT-4.


- Results

  <img src="" width="1000">

  <img src="" width="1000">

  <img src="" width="1000">
  
  <img src="" width="1000">
