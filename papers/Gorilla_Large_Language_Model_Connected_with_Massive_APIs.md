# Paper

- Title: Gorilla: Large Language Model Connected with Massive APIs
- Authors: Shishir G. Patil, Tianjun Zhang, Xin Wang, Joseph E. Gonzalez
- Link: https://arxiv.org/abs/2305.15334
- Repository: https://github.com/ShishirPatil/gorilla, https://gorilla.cs.berkeley.edu/
- Tags: API models, LLMs with tools
- Year: 2023

# Summary

- What
  
  - They introduce Gorilla, a finetuned LLaMA-7B-based model that can call a very large number of APIs. The authors claim Gorilla beats GPT-4 at writing API calls.
  - They also introduce APIBench, a new dataset comprising APIs from HuggingFace (925 API calls), TorchHub (94 API calls) and TensorHub (696 API calls).
  - Note that the paper does not execute API calls, they also check if the correct ones are called.

<img src="https://github.com/lisaalaz/papers/blob/master/images/Gorilla_overview.png" width="500">

- How

  - Dataset
    - Note that the APIs in APIBench can be overlapping in terms of what task they solve.
    - To construct APIBench, they:
  
      1) record the model cards for HuggingFace Model Hub, PyTorch Hub ad TensorFlow Hub models. Note that for HF they only select the top 20 models for each domain, due to many of them having poor or no documentation). Generally, they filter out models with poor documentation from the other two sources as well;
      2) convert these model cards into a json file with fields {domain, framework, functionality, api_name, api_call, api_arguments, environment_requirements, example_code, performance, description}. Note that these fields can generalise to other domains than just ML (e.g. RESTful API);
      3) use GPT-4 with self-instruct to generate 10 synthetic user questions for each API. Generation is done by providing the model with 3 in-context exemplars and the API documentation;
      4) use the above generated user questions to create 10 instruction-API pairs for each API, and insert them into the dataset. 
  
  - Model
    - They instruction-finetune a LLaMA-7B model. They use the dataset pairs to create a 2-turn user-agent conversation (1 turn each).
    - They try training the model with and without a retriever (they try BM25, GPT-Index as well as oracle retrieval). They do so by appending to the model prompt the text "Use this API documentation for reference: <retrieved_API_doc_JSON>", where the latter is replaced with the actual retrieval. In doing so, the model needs to parse this part of the prompt to answer the preceding question.
    - With retrieval, the LLM better adapts to test-time changes in the API documentation, though retrieval sometimes hurt performance.
    - At inference time, the process is different for the retrieval-augmented mode and the zero-shot mode:

      1) When in zero-shot mode, the user's prompt (e.g. “I would like to identify the objects in an image”) is fed into Gorilla, which will return the API call.
      2) When in retrieval-augmented mode, the retriever retrieves an API documentation (from an API database), concatenates this to the user prompt (via the text string shown above) and this is all passed into Gorilla. Again, the model then outputs the API call.
  

- Evaluation

  - They define as hallucination the call of APIs that do not exist. When the model calls a wrong API that does exist, this is simply an error/inaccuracy.
  - They adopt the AST tree-matching strategy to check whether the API being called by the model is correct or not for that prompt. This is a necessary step since multiple APIs carry out the same function and thus there can be multiple correct answers. API can have many arguments so matching needs to be done on each of them (to the reference API)

<img src="https://github.com/lisaalaz/papers/blob/master/images/Gorilla_AST.png" width="500">

  - They evaluate on APIBench and compare to zero-shot baselines given by GPT-4, GPT-3.5-turbo, Anthropic's Claude and LLaMa-7B, and find:
    
    1) Gorilla is the best performing model in terms of accuracy on API call on TorchHub and HuggingFace and is at least as good as the other models n TensorflowHub. Note that AST accuracy is used for evaluation apart from the case of HuggingFace, where only if the model has provided the correct domain name is checked (since the dataset is not exhaustive).
    2) Lightly fine-tuned Gorilla is the best performing setting, beating all other models by a large margin (20.43% better than GPT-4, 10.75% better than ChatGPT and 83% over LLaMA). The authors conclude from this that in this context, finetuning is better than retrieval.

  <img src="https://github.com/lisaalaz/papers/blob/master/images/Gorilla_AST_acc_plot.png" width="500">

  <img src="https://github.com/lisaalaz/papers/blob/master/images/Gorilla_AST_acc_table.png" width="500">

    3) Finetuning without retriever and then using the oracle retrieval in the evaluation is inconsisent in the performance results (0.88% worse in TensorHub and 0.97% better in HuggingFace). When not using the oracle at inference, the results are much worse (21.50% in Torch Hub and 47.57% in HuggingFace), as retrieval error propagates to the generated API call.
    4) Finetuning Gorilla with gold retrievals (oracle) achieves higher results than training without retrieval (12.37% better in Torch Hub and 23.46% better in HuggingFace). On the other hand, imperfect retrieval (i.e. non-oracle) still degrades performance at inference (using GPT-Index gives 29.20% degradation, and BM25 results in 52.27% accuracy degradation). However, the authors point out that a better retriever would still do well.
  
  <img src="https://github.com/lisaalaz/papers/blob/master/images/Gorilla_retrieval_comparison.png" width="500">
  
  - In addition, the authors ensure at training that Gorilla is resilient to changes in the documentation at test time (which they call 'retrieval-aware-training).

  <img src="https://github.com/lisaalaz/papers/blob/master/images/Gorilla_retrieval_aware_training.png" width="500">

  - Finally, they evaluate Gorilla's ability to respect constraints given in the prompt (e.g. accuracy, number of learnable parameters in the model, the size on disk, peak memory consumption, FLOPS, etc.). The authors find that accuracy drops across all models in this case (both in zero-shot and retrieval-augmented setting). On the other hand, Gorilla augmented with retrieval matches GPT-3.5 (the best performing baseline).

  <img src="https://github.com/lisaalaz/papers/blob/master/images/Gorilla_constraints_eval.png" width="500">