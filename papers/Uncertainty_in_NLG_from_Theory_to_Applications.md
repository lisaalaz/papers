# Paper

- Title: Uncertainty in Natural Language Generation: From Theory to Applications
- Authors: Joris Baan, Nico Daheim, Evgenia Ilia, Dennis Ulmer, Haau-Sing Li, Raquel Fernández, Barbara Plank, Rico Sennrich, Chrysoula Zerva, Wilker Aziz
- Link: https://arxiv.org/abs/2307.15703
- Repository: unavailable
- Tags: Calibration, Robustness, Alignment, Confidence, Uncertainty
- Year: 2023

# Summary

- What
  
  - They present a theoretical framework and taxonomy to represent uncertainty in NLG.
  - They ...
  - Potential applications of estimating uncertainty in NLG include having a model abstain from answering if it is not confident enough, ask clarificatory questions, defer a decision, predict a distribution over different interpretations or perspectives, do controllable generation to cater to a particular user base.

- Theory

  - An agent shall represent the state of its knowledge in a way that is *sufficient* for reasoning about the truth value of any claim that the agent itself makes about the world.
  - An agent is able to state its preference for claims that it possesses better information (and thus it is less uncertain) about.
  - An agent uses this uncertainty representation to interact with the world and inform its action, and updates the representation upon acquiring new knowledge.
  - An agent does not know the state of the world but it assumes it must be one of a collection of possible worlds. This collection is a set $\Omega$ of mutually exclusive worlds, each of which is represented by a symbol or collection of symbols/attributes (e.g. a string). 
  - For a NLG agent, a world is thus a sequence of words, i.e. an utterance. This agent cannot enumerate the extremely large number of all possible valid utterances, so it uses a large enough set of them .
  - A __proposition__ $E_{t}$ is the claim that some property $t(\omega)$ holds in the world $\omega$. A __property__ is something that can be assessed for any one world. Since the agent does not know the state of the actual world, it represents $E_{t}$ as $E_{t} = \lbrace w \in \Omega : t(w) \rbrace \subset \Omega$, that is, using the set of all possible worlds where the property holds.

- How

  - Strategy
    - Given a visual input $x$ (an image or a video) and a text query $q$ about the content of $x$, their framework synthetises a program $z = \pi(q)$ using a program generator $\pi$ with $q$ as input.
    - Then they apply the execution engine $r = \phi(x, z)$ to execute the program $z$ on the visual input $x$ and give the result $r$, which can be either a crop of the original visual input $x$ or a text string.
  
  - Architecture
    
    - They specify an API containing methods that call existing pretrained models to compute their outputs. For example, they use the MiDaS model for computing the depth in an image. For queries that require an external KB, they have a dedicated method that queries the GPT-3 text model to return the text response to a given query.
    - They use the code LLM GPT-3 Codex to instantiate the program generator $\pi$ (no fine tuning required). They feed to it a prompt containing the query and the API specification, omitting the full implementation of each function and only including the function signatures and docstrings. This model outputs a Python function definition given as a string.
    - They then compile and execute the output string from the previous step (that is, the program $z$). This takes the input image or video $x$ and outputs the result $r$ which answers the query $q$. The execution of $z$ is a simple Python call, and it can leverage not just the given API functions but also any inbuilt function, common Python modules such as math or datetime, and Python control structures.

   <img src="https://github.com/lisaalaz/papers/blob/master/images/ViperGPT_architecture.png" width="500">

- Results

  They evaluate on several tasks:
  - Visual grounding (i.e. identifying the bounding box in an image that best corresponds to a query). For this they evaluate on RefCOCO and RefCOCO+.

    <img src="https://github.com/lisaalaz/papers/blob/master/images/ViperGPT_RefCOCO_eval.png" width="400">

  - Compositional image question answering (which requires decomposing complex queries into simple tasks). For this they evaluate on GQA.

    <img src="https://github.com/lisaalaz/papers/blob/master/images/ViperGPT_GQA_eval.png" width="400">

  - External knowledge-dependent image question answering, evaluated on OK-VQA.

    <img src="https://github.com/lisaalaz/papers/blob/master/images/ViperGPT_OK-VQA_eval.png" width="330">

  - Video causal/temporal reasoning, evaluated on NExT-QA.

    <img src="https://github.com/lisaalaz/papers/blob/master/images/ViperGPT_NExT-QA_eval.png" width="420">

  They also show their framework can answer diverse queries not included in the above benchmarks, such as those below. 

![image](https://github.com/lisaalaz/papers/assets/89645136/fe249ed3-7a86-4d8d-beae-381066dd5eba)

  
