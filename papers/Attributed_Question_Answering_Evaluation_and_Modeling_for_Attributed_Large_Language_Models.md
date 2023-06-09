# Paper

- Title: Attributed Question Answering: Evaluation and Modeling for Attributed Large Language Models
- Authors: Bernd Bohnet, Vinh Q. Tran, Pat Verga, Roee Aharoni, Daniel Andor, Livio Baldini Soares, Massimiliano Ciaramita, Jacob Eisenstein, Kuzman Ganchev, Jonathan Herzig, Kai Hui, Tom Kwiatkowski, Ji Ma, Jianmo Ni, Lierni Sestorain Saralegui, Tal Schuster, William W. Cohen, Michael Collins, Dipanjan Das, Donald Metzler, Slav Petrov, Kellie Webster
- Link: https://arxiv.org/abs/2212.08037
- Repository: https://github.com/google-research-datasets/Attributed-QA
- Tags: Attributed LMs, Question answering, QA
- Year: 2023

# Summary

- What
  
  In this paper they propose:
  - An evaluation framework for attributed question answering (QA).
  - An analysis of several existing systems based on this framework.

<img src="https://github.com/lisaalaz/papers/blob/master/images/Attributed_QA_example.png" width="400">
  
- How

  - Evaluation framework
    - They define the task of attributed QA as follows:
      - Given a set $C$ of id and document key-value pairs and an input question $x$, the output of an attributed QA system $g$ is a pair $g(x) = (a,c)$ where $a$ is a text string and $c$ is a member of $C$.
    - Their evaluation framework consists of:
      - Human evaluation (AIS): given a triple $(x, a, c)$, human raters are asked to answer the questions: (1) Is all of the information relayed by the system response $(c, a)$ interpretable to you? (2) Is all of the information provided by the system response $a$ fully supported by the source document $c$? The rating of $(x, a, c)$ is thus classified as attributable if the answer is yes to both questions. As several raters score the same example, that example is rated 1 if the majority of them has classified it as attributable, and 0 otherwise. The test accuracy of an attributed QA system $g$ is thus simply measured as the proportion of test examples rated 1.
      - Automated evaluation (AutoAIS): uses a T5 model finetuned for NLI to determine whether the question $x$ and the answer $a$ are entailed by the provided attrbution $c$. Similarly to the human evaluation case, a rating of 1 means the example is classified as attributable and a rating of 0 means it is not, and the test accuracy is measured as the proportion of examples rated 1 in the test set.
    
  - System analysis
    - They classify the following architectures:
      - Retrieve-then-read (RTR): these systems first retrieve k relevant passages based on the input question then p out of k are taken by the model as input to generate the answer, and one passage is chosen to support that answer.
      - Post-hoc retrieval: in these systems first an LLM is used to generate an answer to the input question (typically with few-shot prompting), then the question and the answer are concatenated together and used as a query to retrieve k relevant passages. Finally, the highest-scoring passage containing the answer generated by the LLM is selected and used as attribution.
      - LLM-as-retriever: in these systems an LLM is used to generate both an answer and a pointer into the attribution corpus through a combination ofnprompting and finetuning (but without any retrieval). In the case they consider, the LLM first generates a webpage URL from which a paragraph is selected to support the answer.
    
- Results

  - They find that the best-performing RTR system uses GTR (Ni et al., 2021) to retrieve k=50 passages based on the input question and an NQ-reranker to rerank these passages. Then, FID (Izacard et al., 2022) is trained with T=50 passages and generated an answer based on the top P=1 passage, which is also returned as the attribution.
  - They find that the best post-hoc retrieval system uses a PaLM model (540B parameters) prompted with 64 question-answer pairs from the Natural Questions train set to produce an answer to the input question. Then GTR is used to retrieve an attribution using the concatenation of the question and answer as the query and selecting the passage in the top k=50 that contains the string generated by PaLM.
  - They find that the best LLM-as-retriever system consists of a PaLM (540B) model finetuned on a corpus of questions generated by a model trained on SQUAD over decontextualised Wikipedia sentences, and the Natural Questions train set. This model generates an answer and a Wikipedia URL given the input question, then the paragraph in the Wikipedia article that has the highest BM25 score is used as the attribution.
  - They find that systems which perform best on human evaluation do not necessarily achieve the best exact match (EM) accuracy.
  - They also find that the best RTR system achieves the highest performance overall (in both AIS and AutoAIS).

<img src="https://github.com/lisaalaz/papers/blob/master/images/Attributed_QA_results.png" width="450">

  
