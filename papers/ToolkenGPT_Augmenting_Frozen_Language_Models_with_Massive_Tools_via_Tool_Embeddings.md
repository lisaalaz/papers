# Paper

- Title: ToolkenGPT: Augmenting Frozen Language Models
with Massive Tools via Tool Embeddings
- Authors: Shibo Hao, Tianyang Liu, Zhen Wang, Zhiting Hu
- Link: https://arxiv.org/abs/2305.11554
- Repository:  https://github.com/Ber666/ToolkenGPT
- Tags: LLMs with tools, API models
- Year: 2023

# Summary

- What
  
  - They introduce a new approach to finetuning LLMs for tool usage.
  - Their new approach represents each tool as a token and learns an embedding for it.
  - In this way, calling a tool works in the same way as generating a word token.
  - Once a tool token ('toolken') is generated, the LLM is prompted to add arguments for the API call.
  - This method, they claim, is flexible and allows adding new tools on the go by expanding the set of toolkens.
  
- How

  - Each toolken is inserted into the LLM head just like a regular word token embedding.
  - At generation, every time a toolken is decoded, the LLM switches into a special mode where it produces input arguments for the tool execution via prompting. The output of the tool execution are then inserted back into the generation. This is showin in Fig. 1.
  
  %fig 1 here

- Training
  
  - Each toolken is parametrised as a toolken embedding vector, and a set of toolken embeddings is a matrix $W_{\tau} \in \R^{|\Tau| \times d}$ where $\Tau = \lbrace {\tau}_1, {\tau}_2, ... \rbrace$ is the set of tools.
  - The above matrix is concatenated with the vocabulary matrix $W_{v}$
  - Apart from $W_{\tau}$ concatenated at the LLM head, the rest of the LLM parameters remain frozen.
  - Moreover, unlike prompt or prefix tuning, it does not require to backpropagate all the way back through the main LLM body. This means that training is not much more computationally intensive than inference.
  - For this reason, it is easy to expand the matrix adding new tools and tune it again.

  - Training data:
  
    - From the example in Fig. 1, we have the sentence “the area is 256 square feet ...”. This is tokenized into the sequence $s = (“the”, “area”, “is”, “2”, “5”, “6”, “square”, “feet”, ...)$ and this is converted to $s
    ' = (“the”, “area”, “is”, “[square]”, “[N/A]”, “[N/A]”, “square”, “feet”, ...)$, where the subsequence $(“2”, “5”, “6”)$ is masked with a sequence of equal lenghth with the tool call in first position and $“[N/A]”$ in the other positions. Note that the $“[N/A]”$ are used as mask so the LLM can ignore them in training, thanks to a modified training objective (see below).
    - The dataset they use to finetune is composed of these parallel sequences, $D = \lbrace (s, s') \rbrace$
    - The training objective is to minimise $L = {\sum}_{(s, s') \in D} {\sum}^N_{i=1} - \log P(t'_i | t_{<i}) \textbf{1}_{t'_i \neq \text{[N/A]}}$, where the last part is simply a switch to ignore the $“[N/A]”$ tokens while training.

  - To get the training data, they do not just use existing datasets but also explore creating a new dataset using LLMs, via in-context learning (this is not dissimilar to how they make the data in Toolformer).

- Inference
  
  - When in 'reasoning mode', the LLM generates the next token in the sequence with probability $P(t_i | t_{< i}) = \text{softmax}([W_v ; W_{\tau}] \cdot h_{i-1})$ where $;$ is the concatenation operator (i.e. the token can either be a word token or a toolken). New tools can be added by expanding the toolken embedding matrix.
  - When in 'tool mode' (i.e. after a toolken has been generated) the LLM appends the current generated context to a prompt that shows how to generate tool arguments for that tool. It then generates the arguments for the current tool.
  - The tool is thus executed, and the returned value appended to the text. the LLM returns to the reasoning mode.

- Results
  
  - They apply ToolkenGPT on the following tasks:
    
    1) Numerical reasoning (GSM8K-XL and FuncQA datasets),
    2) Knowledge-based Question Answering (KAMEL dataset),
    3) Embodied Plan Generation (VirtualHome dataset)

The results of the experiments and comparison against the baselines are below.