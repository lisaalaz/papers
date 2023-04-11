# Paper

- Title: Dynamic Knowledge Routing Network For Target-Guided Open-Domain Conversation
- Authors: JJinghui Qin, Zheng Ye, Jianheng Tang, Xiaodan Liang
- Link: https://arxiv.org/abs/2002.01196
- Repository: https://github.com/James-Yip/TGODC-DKRN
- Tags: Target-Guiding, Chatbots
- Year: 2020

# Summary

- What
  
  - A system for target-guided open-domain conversation relying on a dynamic knowledge routing network (DKRN) that considers semantic knowledge relations between candidate keywords.
  - Their approach augments [Tang et al.'s](https://github.com/lisaalaz/papers/blob/master/papers/Target-Guided_Open-Domain_Conversation.md).
  - They introduce a [new dataset](https://drive.google.com/file/d/1NYBLxkLnGRNv720SLIcQyX7Um6rbxsAc/view) of more than 900k conversations in Chinese.

- How

  - Strategy
    - The chat starts from any conversational topic chosen by the user.
    - The conversation target, i.e. a keyword, is unknown to the user.
    - The agent has access to the entire conversation history as a sequence of utterances (from both parties).
    - The next utterance must satisfy: (a) __transition smoothness__ (i.e. it must be natural and appropriate for the current conversation context) and (b) __target achievement__ (i.e. it must drive the conversation toward the target).
    - The target is considered to have been achieved when either the user or the agent mentions it in an utterance.
  
  - Architecture
    
    The system is made of the following components:
    - A dynamic knowledge routing network (DKRN), agnostic to the end target and trained on a keyword-annotated chit-chat dataset, branching into:
      1) A basic keyword prediction branch that encodes the conversation history $h_t$ at time *t* (they use an RNN) and feeds the resulting embedding to two continuous fully-connected layers that ouput a distribution over candidate keywords $K_{t+1}$ for the next conversation turn $t+1$. Thus the distribution over candidate keywords is given by $K_{k+1} = fc_2(\sigma(fc_1(RNN(h_t))))$ where $\sigma(\cdot)$ is a ReLU activation. 
      2) An explicit knowledge routing branch that utilizes the relation graph between candidate keywords built on the keyword-annotated chit-chat dataset. For each consecutive utterance pair in the dataset, they extract the keywords and join these by a directed edge in the graph. At time *t* in the conversation, the explicit knowledge router extracts the set of relevant keywords $S_t$ and finds in the relation graph all the keywords $R_t$ that are relevant to $S_t$. From this it generates a mask vector $M_{t+1}$ that has the same entries in the same order as the candidate keywords in $K_{t+1}$. In this mask vector the positions of the keywords present in $R_t$ are set to 1 and the others to $-1e8$. The distribution over the candidate keywords is thus recomputed as $P_{t+1} = \sigma(K_{t+1} - 1 + M_{t+1})$ where $\sigma(\cdot)$ is a sigmoid activation.
    - A dual discourse-level target-guided strategy aimed at guiding the topic of the conversation toward the end target. Similarly to Tang et al., the keyword chosen for the next turn $t+1$ must be closer to the target than the current keyword at turn *t*. In addition, the next response at turn $t+1$ must either contain the predicted keyword (i.e. the one with the highest probability in the distribution $P_{t+1}$) or a keyword even closer to the target. To this end, the candidate keywords in the distribution $P_{t+1}$ are sorted by probability and a set of candidate utterances are retrieved (using the same retrieval method as in Tang et al.). The most likely utterance that contains *either* the most likely keyword *or* a keyword in the distribution whose distance to the target is even smaller is chosen.

![architecture](https://user-images.githubusercontent.com/89645136/231024854-ce1a613a-db13-4d9a-ab88-7c7090b0eb2c.png)
  
- Results

  - They evaluate the system with self-play simulation, where one vanilla system plays the user by retrieving the next response without knowledge of the end target *t*. Another agent tries to guide the conversation toward *t*. In TGPC, the target is achieved whenever the word *t*, or its WordNet synonym (defined as a word that has content similarity score > 0.9), is mentioned in the conversation. In CWC the target is achieved when the exact target word is mentioned. The maximum allowed number of turns in the conversation is eight (if an agent does not reach the target by then, it is counted as a failure). They measure the success rate (reaching target by the 8th turn) and the average number of turns it takes to reach *t*.

<img src="https://user-images.githubusercontent.com/89645136/231024925-ae98cf0c-c4b9-4443-92ed-47874383a8bc.png" width="600">
  
  - They also carry out human evaluation for the same metrics as above.

<img src="https://user-images.githubusercontent.com/89645136/231025033-30a5a94a-b283-44a8-b28d-f45069a75c51.png" width="550">
  
- Example conversations
  
<img src="https://user-images.githubusercontent.com/89645136/231024991-b39da7a8-f66a-43bb-a6a3-d94c43547e6a.png" width="600">
