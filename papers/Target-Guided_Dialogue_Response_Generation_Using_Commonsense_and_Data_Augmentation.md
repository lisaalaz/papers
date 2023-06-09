# Paper

- Title: Target-Guided Dialogue Response Generation Using Commonsense and Data Augmentation
- Authors: Prakhar Gupta, Harsh Jhamtani, Jeffrey P. Bigham
- Link: https://arxiv.org/abs/2205.09314
- Repository: https://github.com/prakharguptaz/target-guided-dialogue-coda
- Tags: Target-Guiding, Knowledge Graphs, Chatbots
- Year: 2022

# Summary

- What
  
  - A technique to generate chatbot responses in a target-guided manner, referred to as CODA (Commonsense Path and Data Augmentation).
  - The proposed approach bridges the current state of the conversation to the target through a path of commonsense knowledge concepts, and uses these to generate transition responses.
  - They propose a new automated evaluation metric, called Target-Coherence, that evaluates transition smoothness.

- How

  - Strategy
    - In a conversation between two speakers A and B, given a context *c* and a target utterance *t* for speaker B, the system should generate a transitition utterance *s* for speaker B, which should contribute toward a smooth transition from *c* to *t*.
    - They [repurpose the DailyDialog chit-chat dataset](https://drive.google.com/drive/folders/1xuzHVqoLXUkD9XCjWpB5zFsJlBvKetAT) for the task of target-guided response generation, making it similar in structure to the Otters dataset. The latter is a dataset of single-turn conversations made up of a context utterance $u_a$ from speaker A, a target $u_b$ from speaker B, and a transition utterance *s* for B to serve as a smooth transition link between $u_a$ and $u_b$.
  
  - Architecture
    
    The system is made of the following two components:
    - A knowledge path generator (KPG) that comes in two options:
      1) KPG-head-tails (KPG-ht) - this is a GPT-2 finetuned on a dataset of random walks sampled from the semantic graph ConceptNet to predict a path *p* given a head entity $n_h$ (extracted from the context *c*) and a tail entity $n_t$ (extracted from the target *t*). A path $p = \lbrace n_h, e_0, n_1, e_1, ..., e_{k-1}, n_t \rbrace$ of length *k*, sampled from the set *P* of all paths in the graph, where $n_i$ are intermediate nodes in the path and $e_i$ are edges, is formatted as the following sequence for the language model: "[target] $n_t$ [sep] $n_h e_0 n_1 e_1 ... e_{k-1}$". KPG-ht takes the head and tail entities $n_h$ and $n_t$ as input and generates a commonsense path.
      2) KPG-will-contain (KPG-wc) - also a GPT-2 model (in reality this is the same model trained for both the above task and this task) finetuned to generate a path from the graph that contains entities from the gold response. A pre-determined entity set $E_p = \lbrace k_1, ..., k_n \rbrace$ contains the entities in the path (that is, $n_1, ..., n_{k-1}$) in randomly permuted order. These are prepended to the sequence which becomes "[wc] $k_1$ [wc] $k_2$ ... [target] $n_t$ [sep] $n_h e_0 n_1 e_1 ... e_{k-1}$". Training using this augmented sequence teaches the model that a suitable generated path should contain the elements of $E_p$ in sensible order. KPG-wc takes as input a head and tail entity pair $n_h$ and $n_t$ and the entity set $E_p$ of randomly permuted gold transition entities in $E_r$, and outputs a set of paths containing the head and tail entities as well as the gold transition entities.
      Note that the KPG component resolves a known sparsity issue in ConceptNet: as the paths are generated rather than sampled from a fixed KB, they are not limited to ConceptNet but can contain other knowledge.
    - A commonsense response generator (CRG), that is, a GPT-2 conditioned on the commonsense knowledge path generated by the KPG. To train this model, for each example in the Otters dataset they extract three sets of entities from $u_a$ (context entity set $E_h$), $u_b$ (target entity set $E_t$) and *s* (transition entity set $E_r$) respectively. From every pair of entities $e_h^i$, $e_t^i$ in $E_h$ and $E_t$ respectively, multiple relevant paths from $e_h^i$ to $e_t^i$ are sampled from ConceptNet using KPG-wc. At inference, as the gold transition entitity set $E_t$ is not available, KPG-ht is used instead. To avoid having an excessively large search space, not all pairs $e_h^i$, $e_t^i$ are considered and paths are also further filtered to result in a final, smaller set of suitable paths. The CRG takes as input a sequence of the form "knowledge path [target] target sentence [context] context sentence [response] transition response" for each knowledge path from the set P, and generates a transition response. At inference only a random path *p* among the generated ones is chosen. 
    - A model for outputting the Target-Coherence metric score. This model, a GPT-2, is trained to classify a transition response as either positive (i.e. coherent to the context and smooth toward the target) or negative (either incoherent or not smooth). To train it, they use the examples in the Otters dataset as positives and artificially create negative examples from the data for training.

![system architecture](https://user-images.githubusercontent.com/89645136/231242092-5147386e-0d3a-4d29-adde-1f618be8d0e2.png)

- Results

  - They show that CODA outperforms the baselines in automated evaluation (TC refers to their Target-Coherence metric in the table below).

![automated eval](https://user-images.githubusercontent.com/89645136/231242554-96bfa8c2-24fd-4202-9dca-411a3b94e9cc.png)
  
  - They also carry out a comparative human evaluation of their system vs. the baselines for smoothness, sensibility and informativeness.

<img src="https://github.com/lisaalaz/papers/blob/master/images/Target-Guided_Dialogue_Response_Generation_Using_Commonsense_and_Data_Augmentation_human_eval.png?raw=true" width="500">

- Example conversations

<img src="https://github.com/lisaalaz/papers/blob/master/images/Target-Guided_Dialogue_Response_Generation_Using_Commonsense_and_Data_Augmentation_examples.png?raw=true" width="600">

  
