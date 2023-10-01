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
