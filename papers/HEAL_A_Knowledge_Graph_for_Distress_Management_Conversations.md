# Paper

- Title: HEAL: A Knowledge Graph for Distress Management Conversations
- Authors: Anuradha Welivita, Pearl Pu
- Link: https://ojs.aaai.org/index.php/AAAI/article/view/21398/21147
- Repository: https://github.com/anuradha1992/HEAL
- Tags: Knowledge Graphs, Chatbots, Mental Health
- Year: 2022

# Summary

- What
  
  - A knowledge graph for distress management conversations.
  - The graph contains 22,037 nodes representing:
    - ~4k stressors (causes of mental distress) such as Suicidal ideation, Anxiety attacks, Weight gain, Loneliness, Failing college, Alchohol addiction, US elections and Covid-19.
    - ~3k expectations (questions that are commonly asked by people experiencing distress)
    - ~13k response types (frequent types of responses given by the listener to the person experiencing distress)
    - ~1.2k feedback types (frequent types of feedback provided by the original speaker after the listener has given their response)
  - Each node also has an associated affective state, i.e. an emotion that best captures its text content.
  - The graph also contains 104,004 edges between the different types of nodes.
  - The knowledge encoded in the graph tells us whether a particular response elicits a positive or negative response, and whether it addresses one of the speaker's expectations. This can lead to selecting better responses.

<img src="https://github.com/lisaalaz/papers/blob/master/images/HEAL_overview.png?raw=true" width=500>

- How

  - The graph is made from 1M+ dialogues about distressing situations taken from the following sub-Reddits: *mentalhealthsupport; offmychest; sad; suicidewatch; anxietyhelp; depression; depressed* and *depression_help*. Curse words and profanities have been removed. 80 percent of the resulting conversations is used to build the graph and the remaining 20% is kept for validation and testing of downstream tasks.
  - The conversation data goes through a pipeline consisting of:
    - Summarization: as the conversations are artificially created from Reddit, some turns are very lengthy. Long turns of >= 100 tokens are summarized using the SMMRY algorithm for extractive summarization.
    - Clustering: agglomerative clustering (Murtagh and Legendre, 2014) with cosine similarity between SentenceBERT embeddings as the distance metric is employed to identify types of stressors, expectations, responses and feedback types.
    - Topic modelling, question extraction, representative selection and filtering are used to aid the clustering of the stressors, expectations, response types and feedback types respectively.
    - Emotion recognition: a BERT-base classifier trained on EmpatheticDialogues (acc. ~0.66) is used to classify the extracted texts as associated to one of 41 possible emotions. All the texts in a cluster are classified in this way and the majority emotion is taken as the emotion representative of the cluster/node and appended to it.
  
![construction process](https://user-images.githubusercontent.com/89645136/231503265-1358e94a-3456-458f-9dfa-755b7da2a6e3.png)

  - The resulting KG is cluster-based: each stressor is a cluster containing several narratives (i.e. dialogue prompts where a person expresses distress) and the other elements connected to the stressor (expectations, responses and feedback) are also clusters contaning several texts from the original data. The edges in the graph have a weight representing the number of connections between elements in each of their end clusters/nodes.

![visualisation](https://user-images.githubusercontent.com/89645136/231502379-2bc6f608-9685-4864-91f3-483d74be8df7.png)

![statistics](https://user-images.githubusercontent.com/89645136/231502318-11ad016c-51fc-4146-b020-2058ece08a0b.png)

- Results
  
  - They use 10% of the original Reddit dialogue dataset to test the performance of the KG for retrieving appropriate empathetic responses given a dialogue prompt. They compute the cosine similarity between the given prompt and the narratives in each cluster and choose the cluster containing the most similar narrative.
  
  - They then rank the responses associated with that cluster by their edge weight (only looking at the edges between the prompt cluster and the response cluster), and select the top one.
  
  - They evaluate this response method, called HEAL-ranked, on several automated metrics, along with responses given by Xie and Pu's model (2021) and the generative chatbot Blender (Roller et al. 2021). The results are below.
  
![automated evaluation](https://user-images.githubusercontent.com/89645136/231503488-f5b97c64-58d1-4e50-8bd9-162e832c7e9c.png)

  - They also carry out human evalation on 200 randomly selected dialogues from the test set, comparing the same three models. The results are below.
  
<img src="https://github.com/lisaalaz/papers/blob/master/images/HEAL_human_eval.png?raw=true" width=500>
