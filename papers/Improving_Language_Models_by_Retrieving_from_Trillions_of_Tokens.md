# Paper

- Title: Improving Language Models by Retrieving from Trillions of Tokens
- Authors: Sebastian Borgeaud, Arthur Mensch, Jordan Hoffmann, Trevor Cai, Eliza Rutherford, Katie Millican, George van den Driessche, Jean-Baptiste Lespiau, Bogdan Damoc, Aidan Clark, Diego de Las Casas, Aurelia Guy, Jacob Menick, Roman Ring, Tom Hennigan, Saffron Huang, Loren Maggiore, Chris Jones, Albin Cassirer, Andy Brock, Michela Paganini, Geoffrey Irving, Oriol Vinyals, Simon Osindero, Karen Simonyan, Jack W. Rae, Erich Elsen, Laurent Sifre
- Link: https://arxiv.org/abs/2112.04426
- Repository: https://github.com/lucidrains/RETRO-pytorch (unofficial implementation)
- Tags: Retrieval-augmented generation
- Year: 2021

# Summary

- What
  
  - A retrieval-enhanced model (RETRO) combining a medium-sized transformer LM (25x less parameters than GPT-3), a 2-trillion token database, and a frozen BERT retriever.
  
- How

  - Architecture
    - The key-value database stores continguous token chunks (rather than individual tokens). Keys are BERT embeddings of these text chunks and values consist of the raw text version of these chunks plus the continuation text. To compute the embeddings they use a frozen BERT model.
    - The train and retrieval data originates from the same dataset.
    - They use the ScaNN library to query the DB (ScaNN can query a DB of N elements in approximately O(log N) time) and $L_2$ distance.
    - The LM is an encoder-decoder transformer which performs cross-attention on the retrieved data. 
    - Its encoder encodes not just the input but also the retrieved neighbouring chunks. The encoding of the $j^{th}$ neighbour of the $u^{th}$ chunk, that is $RET(C_u)^j$, depends on the attended activation $H_u = (h_{(u-1)m+i})_{i \in [1,m]}$, which has size $m \times d$.
    - Its decoder is made of standard transformer blocks as well as RETRO blocks. The RETRO blocks include a chunked cross-attention (CCA) layer that incorporates the information from the encoded retrievals. To perform CCA first an intermediate activation $H \in \R^{n \times d}$ is split into $l-1$ attending chunks, then the layer computes the cross-attention between the embedding of the last token in the previous chunk $C_u$ and those of the first $m-1$ tokens of the current chunk $C_{u+1}$ and the encoded neighbours of $C_u$ (denoted by $E_u$). The $l-1$ outputs of CCA are then concatenated.
    - Overall, the RETRO model, parametrized by $\theta$, takes as input both the previous tokens and their neighbours, and produces an output by applying the sequence log-likelihood: 
      
      $L(X|\theta, D) = \sum^l_{u=1} \sum^m_{i=1} \ell_{\theta}(x_{(u-1)m+i} | (x_j)_{j<(u-1)m+i}, (RET_D(C_{u'}))_{u'<u})$ 

      where $D$ is the database, $l$ is the total number of chunks, $m$ is the size of the chunks, $x_{(u-1)m+i}$ is the $i^{th}$ token of the $u^{th}$ chunk, $(x_j)_{j<(u-1)m+i}$ are the previously seen tokens and $(RET_D(C_{u'}))_{u'<u}$ are the neighbours of chunk $C_u'$ which precedes chunk $C_u$.

      
  
  - Strategy
    
    - After tokenizing an input text sequence, the split the sequence of tokens into $l$ chunks, of size $m = {n \over l}$, where $n=2048$ and $m=64$.
    - To avoid the overhead cost of retrieving at training time, all the retrievals are pre-computed into the train set. Each chunk is augmented with its k nearest neighbour chunks from the DB.
    - Before retrieval, they filter out any neighbours that originate from the same document as the current imput sequence (to avoid retrieving the next imput chunk $C_{u+1}$ when retrieving using chunk $C_u$).
    - At training time they also remove from the retrieval corpus all documents that have a 13-gram Jaccard similarity >= 0.8 to a validation or test document. This is to minimise test set leakage.
    - They set $RET(C_1) = \empty$ for the first chunk. In other words, the likelihood of tokens generated from the first chunk does not depend on any retrievals.

- Results

  - For the task of language modelling, they show that RETRO outperforms vanilla transformers of same size, for all model sizes and on all tested datasets (C4, Wikitext-103, Curation Corpus, Lambada, Pile).
  - They show that scaling up the size of the DB improves performance, and performance also increases as the number of retrieved neighbours is increased from 1 to 10 (this only happens at inference, as the model is always trained with 2 retrieved neighbours).
  - They compare their 7B RETRO model with Jurassic-1 (178B parameters) and Gopher (280B), on the Pile test sets, and show RETRO outperforms Jurassic-1 on the majority of test sets and also outperforms Gopher on more than half of them, despite being much smaller.
  - They experiment with their model on question-answeing tasks and they compare it to KNN-LM ([Khandelwal et al., 2020](https://github.com/lisaalaz/papers/blob/master/papers/Generalization_through_Memorization_Nearest_Neighbor_Language_Models.md)), on the Wikitext-103 dataset (results in the figures below).
  - Finally, they show that any pretrained transformer can be finetuned in RETRO fashion, by freezing the pretrained weights and only training the CCA and neighbour encoder parameters (after having intialised them randomly). They show that RETRO-fitting pretrained models surpasses the baseline and comes close to the performance of the RETRO models trained from scratch. 


  
