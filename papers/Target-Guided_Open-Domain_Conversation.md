# Paper

- Title: Target-Guided Open-Domain Conversation
- Authors: Jianheng Tang, Tiancheng Zhao, Chenyan Xiong, Xiaodan Liang, Eric P. Xing, Zhiting Hu
- Link: https://arxiv.org/abs/1905.11553
- Repository: https://github.com/squareRoot3/Target-Guided-Conversation
- Tags: Target-Guiding, Chatbots
- Year: 2019

# Summary

- What
  
  - A generalizable (i.e. domain-agnostic) method for proactively guiding an open-ended chatbot's conversation toward a designated target, while chatting naturally with the user.
  - They propose a structured approach based on keywords for conditioning the chatbot's responses.
  - They present a new dataset derived from PersonaChat, where each conversation turn has been annotated with keywords. Dataset can be downloaded [here](https://drive.google.com/file/d/1oTjOQjm7iiUitOPLCmlkXOCbEPoSWDPX/view).

- How

  - Strategy
    - The chat starts from any conversational topic chosen by the user.
    - The conversation target (unknown to the user) is defined as a word *t*.
    - The agent has access to the entire conversation history as a sequence of utterances (from both parties) $x_{1:n}= \lbrace x_1, ..., x_n \rbrace$.
    - The next utterance $x_{n+1}$ must satisfy: (a) __transition smoothness__ (i.e. it must be natural and appropriate for the current conversation context) and (b) __target achievement__ (i.e. it must drive the conversation toward the target *t*).
    - The target *t* is considered to have been achieved when either the user or the agent mentions it in an utterance.
  
  - Architecture
    
    The system is made of the following components:
    - A turn-level keyword transition predictor that takes as input the conversation history thus far and outputs keywords that are suitable for the next response. This is a hybrid kernel-based method classifier that combines neural feaure extraction with pairwise closeness measuring. It is trained on a dataset derived from the PersonaChat dataset where the utterances have been annotated with keywords. 19 negative responses for each turn are sampled for training the model. This component is agnostic to the end target *t*.
    - A discourse-level target guiding module that chooses at each step a set of candidate next keywords that are closer to the target *t* than the current keyword. The distance between words is measured by the cosine similarity between normalised embeddings. From this candidate set, the next keyword is picked by turn-level keyword transition predictor.
    - A keyword-augmented response retrieval module to be used with a dataset of candidate responses. This module encodes the chosen keyword, the conversation history and each candidate response (they use an RNN for this), multiplies element-wise each candidate response encoding with the encodings of the keyword first, and then the history, and concatenates the two resulting vectors. Each of these concatenations (for each candidate response) is then fed into a single-unit dense layer with sigmoid activation to obtain the probability of each candidate. The most likely candidate response is chosen.

![system architecture](https://user-images.githubusercontent.com/89645136/230964032-65f3f6b6-6f0f-431c-8108-a2524854da12.png)

- Results

  - They evaluate the system with self-play simulation, where one vanilla system plays the user by retrieving the next response without knowledge of the end target *t*. Another agent tries to guide the conversation toward *t*. The target is achieved whenever the word *t*, or its WordNet synonym (defined as a word that has content similarity score > 0.9), is mentioned in the conversation. The maximum allowed number of turns in the conversation is eight (if an agent does not reach the target by then, it is counted as a failure). They measure the success rate (reaching target by the 8th turn) and the average number of turns it takes to reach *t*.

![automated evaluation](https://user-images.githubusercontent.com/89645136/230963493-ac4e455f-05d4-4725-994c-334485f046ce.png)
  
  - They also carry out human evaluation for the same metrics as above.

![human eval](https://user-images.githubusercontent.com/89645136/230963756-8a619527-84d6-4e46-bd0a-d9c685ab15e9.png)

- Example conversations

![examples](https://user-images.githubusercontent.com/89645136/230963951-0c057e98-c4cb-4288-ae52-6e5665c35b73.png)

  
