# Paper

- Title: KAT: A Knowledge Augmented Transformer for Vision-and-Language
- Authors: Liangke Gui, Borui Wang, Qiuyuan Huang, Alex Hauptmann, Yonatan Bisk, Jianfeng Gao
- Link: https://arxiv.org/abs/2112.08614
- Repository: https://github.com/guilk/KAT
- Tags: Vision-and-language, multimodal, retrieval-augmented generation
- Year: 2022

# Summary

- What
  
  - A multimodal (vision and language) knowledge-augmented transformer (KAT) that integrates implicit and explicit knowledge to answer text questions over images.
  - They define as explicit knowledge the knowledge derived from exernal KBs and implicit knowledge the one stored in the parameters of a LLM.
  
- How

  - Architecture
  
    The KAT model consists of:
    - An explicit knowledge base (KB), identified as $K$, built from Wikidata, covering 8 categories of animals, vehicles and common objects. Each entry in $K$ is a concatenation of the entity name and the corresponding description. This is pre-built using an entity encoder $E_{ent}$, and indexed using the FAISS library. An example entry in the KB would be *<Q2813, Coca-Cola, carbonated brown colored soft drink>*.
    - An explicit knowledge retriever: this is a contrastive-learning based model (a CLIP model in this case) trained to associate image regions with entries in the external KB. They retrieve the closest KB entries from a $d_r$-dimensional space, given an image region as the query. To obtain the image regions, an input image $v_i$ is split into $N$ regions $\lbrace v_i^1, ..., v_i^N \rbrace$ using a sliding window. Then an image encoder $E_{img}(\cdot)$ maps each patch to the $d_r$-dimensional space and retrieves $k$ entries from $K$ whose encodings are closer to the encoded image region, using the inner product of the normalised representations of the region $v_i^j$ and the entity $e$ as distance measure: $sim(v_i^j, e) = E_{ent}(e)^T E_{img}(v_i^j)$. Of the retrieved $N \times k$ entities for a given image $v_i$ ($N$ as there are $N$ regions in the image), only the top $m$ entries are kept to form the explicit knowledge source $x^{exp}$, ranked by similarity score.
    - An implicit knowledge "retriever", which is simply an LLM (a frozen GPT-3 model in this case) prompted to provide implicit knowledge and evidence. For each $(v_i, q_i)$ pair of image and question the following happens: 
      - first $v_i$ is converted into a text description $C$ using an image-captioning model and then this description as well as the question $q_i$ are inserted into a pre-contructed prompt template. A set of context-question-answer triples from the training data that are semantically most similar to $(v_i, q_i)$ are also added to the prompt.
      - The above prompt is fed into the gpt-3 model, and its output is the tentative answer candidate to $(v_i, q_i)$.
      - To obtain also the supporting evidence, the above tentativa answer candidate $a$ and the original question $q_i$ are composed into another prompt template of the form $"(question q_i)? (answer a). This is because"$. This prompt is fed into the same gpt-3 model and the supporting evidence for the $(v_i, q_i)$ pair and the tentative answer $a$ is thus obtained.
      - The tentative answer candidate and the supporting evidence are concatenated and become the implicit knowledge source $x^{imp}$.
    - A knowledge reasoning module, that is, a T5 model that encodes each question and knowledge pair separately, and jointly reasons over $x^{exp}$ and $x^{imp}$. This is done as follows:
      - $q_i$ is concatenated with $x^{exp}$ and $x^{imp}$ separately to form question-knowledge pairs. "question", "entity" and "description" are added before the elements in $x^{exp}$ and "question", "candidate" and "evidence" are added before each element in $x^{imp}$.
      - The two question-knowledge pairs so formed are encoded separately by going through an embedding layer first and then a series of T5 encoder layers.
      - For each pair, they take the token embeddings from the last encoder layer and average them, obtaining an embedding matrix $X^{exp}$ of size $m \times d$ (where $m$ is the number of retrieved KB entries) and an embedding matrix $X^{imp}$ of size $p \times d$ (where $p$ is the number of implicit knowledge entries provided).
      - Then the embeddings in $X^{exp}$ and $X^{imp}$ are concatenated together to form $X \in ℝ^{(m+p) \times d}$ and used as a global knowledge matrix, which is the input to the cross-attention layer.
      - The embeddings of $X^{exp}$ and $X^{imp}$ are fed into a sequence of T5 decoder layers to generate the final answer. To train the model, CE loss is used therefore: $L_{CE} = - \sum_{t=1}^n log$  $p_{\theta} (y_t | y_{{}<t}, x^{exp}; x^{imp})$.
  

- Results

  - KAT obtains SOTA performance on the OK-VQA dataset. Predictions are evaluated against the ground truth (normalised by lowercasing, removing articles, punctuation and duplicated white space).
  - They also show that without the reasoning module, without either of the two knowledge types (implicit and explicit), the model performs worse.
  
