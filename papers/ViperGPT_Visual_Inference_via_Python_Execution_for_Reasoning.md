# Paper

- Title: ViperGPT: Visual Inference via Python Execution for Reasoning
- Authors: Dídac Surís, Sachit Menon, Carl Vondrick
- Link: https://arxiv.org/abs/2303.08128
- Repository: https://github.com/cvlab-columbia/viper
- Tags: Vision-and-language, multimodal, API models
- Year: 2023

# Summary

- What
  
  - They introduce ViperGPT, a framework that leverages existing models to answer queries on images and videos. 
  - Their implementation is centered around GPT-3 Codex that is provided with an API and composes other models, including vision models and also the GPT-3 text model, to answer a given query.

- How

  - Strategy
    - Given a visual input $x$ (an image or a video) and a text query $q$ about the content of $x$, their framework synthetises a program $z = \pi(q)$ using a program generator $\pi$ with $q$ as input.
    - Then they apply the execution engine $r = \phi(x, z)$ to execute the program $z$ on the visual input $x$ and give the result $r$, which can be either a crop of the original visual input $x$ or a text string.
  
  - Architecture
    
    - They specify an API containing methods that call existing pretrained models to compute their outputs. For examples, they use the MiDaS model for computing the depth in an image. For queries that require an external KB, they have a dedicated method that queries the GPT-3 text model to return the text response to a given query.
    - They use the code LLM GPT-3 Codex to instantiate the program generator $\pi$ (no fine tuning required). They feed to it a prompt containing the query and the API specification, omitting the full implementation of each function and only including the function signatures and docstrings. This model outputs a Python function definition given as a string.
    - They then compile and execute the output string from the previous step (that is, the program $z$). This takes the input image or video $x$ and outputs the result $r$ which answers the query $q$. The execution of $z$ is a simple Python call, and it can leverage not just the given API functions but also any inbuilt function, common Python modules such as math or datetime, and Python control structures.


- Results

  They evaluate on several tasks:
  - Visual grounding (i.e. identifying the bounding box in an image that best corresponds to a query). For this they evaluate on RefCOCO and RefCOCO+.

  - Compositional image question answering (which requires decomposing complex queries into simple tasks). For this they evaluate on GQA.

  - External knowledge-dependent image question answering, evaluated on OK-VQA.

  - Video causal/temporal reasoning, evaluated on NExT-QA.

  They also show their framework can answer diverse queries not included in the above benchmarks, such as those below. 


  
