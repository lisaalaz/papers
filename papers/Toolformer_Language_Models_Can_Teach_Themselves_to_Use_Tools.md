# Paper

- Title: Toolformer: Language Models Can Teach Themselves to Use Tools
- Authors: Timo Schick, Jane Dwivedi-Yu, Roberto Dessì, Roberta Raileanu, Maria Lomeli, Luke Zettlemoyer, Nicola Cancedda, Thomas Scialom
- Link: https://arxiv.org/abs/2302.04761
- Repository: https://github.com/lucidrains/toolformer-pytorch (unofficial)
- Tags: LLMs with tools, API models
- Year: 2023

# Summary

- What
  
  - They introduce a new model trained to decide, given a text input, which tools/APIs to call, when, with what arguments and how to incorporate the results into the generation process.
 
<img src="https://github.com/lisaalaz/papers/blob/master/images/Toolformer_examples.png" width="400">

- How
  
  - The ToolFormer model is trained in a self-supervised manner, without requiring much human annotation.
  - This is how the training set is made: given just few human annotation (of how an API can be used), an LLM annotates a large language modelling dataset with potential API calls.
  - Then these annotated calls are filtered as follows: the self supervised loss is used to determine which of these calls are helpful. If they do not reduce the loss, then they are not useful and can be discarded.
  - Once the dataset has been created with the above method, the LLM is finetuned on it.
  - Consider that as the method is dataset-agnostic, the same dataset used for LLM pretraining can be used (to avoid loss of generality).
  - For Toolformer to work, API inputs and outputs need to be able to be represented as text sequences. Special tokens are used to divide these from the rest of the text.
 
    <img src="https://github.com/lisaalaz/papers/blob/master/images/Toolformer_dataset_pipeline.png" width="800">

    <img src="https://github.com/lisaalaz/papers/blob/master/images/Toolformer_prompt.png" width="400">

- Training method

  - They take a language modelling dataset $C$ and augment it with API calls using the language model $M$ and the method show in Fig. 2:
    1) First they sample a large number of potential API calls with $M$ (using the prompt with exemplars illustrated in Fig. 3).
    2) Then they execute the calls.
    3) Then they check whether the obtained responses are useful for predicting future tokens and filter out those that don't. They do this by definining a weighted CE loss $L_i(\textbf{z})=-{\sum_{j=i}}^n w_{j-i} \cdot \log{p_M}(x_j | \textbf{z}, x_{1:j-1})$ for a sequence $\textbf{x}=x_1,...,x_n$, a prefix $\textbf{z}$, a sequence of weights $(w_i | i \in \N)$ and an API call $c_i$ at position $i$ in the sequence with response $r_i$. So in other words, this is the loss of the partial sequence $x_i,...,x_n$. They instantiate two versions of this loss: 
   
       (a) $L_i^{+} = L_i(\textbf{e}(c_i, r_i))$, where $\textbf{e}(c_i, r_i)$ is the API call including the result at position $i$ (this is the weighted loss over the partial sequence if the API call and results are given as a prefix to the model),

       (b) $L_i^{-} = \min(L_i(\epsilon), L_i(\textbf{e}(c_i, \epsilon))$ where $\epsilon$ represents the empty sequence (this is the minimum of the losses of doing no API call or having the API call without response).

       They thus filter the sampled calls by keeping only those for which $L_i^{-} - L_i^{+} >= {\tau}_f$ holds, for a filtering threshold ${\tau}_f$.
    4) Finally they merge API calls for different tools with the data, resulting in the augmented dataset $C^{*}$ (some texts in $C^{*}$ may have multiple API calls).

  - They finetune the language model $M$ on $C^{*}$ using a standard language modeling objective ($L(S) = \sum_i \log p(t_i | t_{i-c},...,t_{i-1}; \Theta)$)

- Inference method
  
  - At inference, they do regular decoding until the token $\rightarrow$ is produced, indicating that $M$ expects an API response. Then decoding is paused and the API call is executed. The response and the ] token are inserted then the decoding resumes.

- Tools used
  
  They use the following tools:
  1) Calculator
  2) Wikipedia search
  3) Machine translator
  4) Calendar

- Results on downstream tasks
  
  They evaluate on the following tasks:

  - Complete a short statement with a missing fact (LAMA)
  - Maths 
  - QA 
  - Multilingual QA 
  - TempLama (queries that change depending on the time) 

  and compare against a GPT-J baseline. Results are below.

  <img src="https://github.com/lisaalaz/papers/blob/master/images/Toolformer_res1.png" width="400"> <img src="https://github.com/lisaalaz/papers/blob/master/images/Toolformer_res2.png" width="400">
  
  <img src="https://github.com/lisaalaz/papers/blob/master/images/Toolformer_res3.png" width="400">

  <img src="https://github.com/lisaalaz/papers/blob/master/images/Toolformer_res4.png" width="800">
