# Paper

- Title: PaLI: A Jointly-Scaled Multilingual Language-Image Model
- Authors: Xi Chen, Xiao Wang, Soravit Changpinyo, AJ Piergiovanni, Piotr Padlewski, Daniel Salz, Sebastian Goodman, Adam Grycner, Basil Mustafa, Lucas Beyer, Alexander Kolesnikov, Joan Puigcerver, Nan Ding, Keran Rong, Hassan Akbari, Gaurav Mishra, Linting Xue, Ashish Thapliyal, James Bradbury, Weicheng Kuo, Mojtaba Seyedhosseini, Chao Jia, Burcu Karagol Ayan, Carlos Riquelme, Andreas Steiner, Anelia Angelova, Xiaohua Zhai, Neil Houlsby, Radu Soricut
- Link: https://arxiv.org/abs/2209.06794
- Repository: unavailable
- Tags: Vision-and-language, multimodal
- Year: 2022

# Summary

- What
  
  In this paper they propose
  - A pathways language and image (PaLI) model that generates text from visual and text inputs in different languages. PaLI is based on a pretrained encoder-decoder LLM and a vision transformer.
  - A new multimodal dataset of image and multilingual text examples - WebLI.
  
- How

  - Architecture
  
    - The PaLI model consists of a vision transformer (ViT) followed by a an encoder-decoder LLM.
    - The visual tokens output by the ViT aew fed into the text encoder of the LLM (no pooling is applied beforehand).
    - Both the ViT and the LLM are initialised to pretrained unimodal checkpoints.
    - PaLI comes in three sizes/versions:
      - PaLI-3B (initialised on ViT-G and mT5-large respectively).
      - PaLI-15B (initialised on ViT-G and mT5-XXL respectively).
      - PaLI-17B (initialised on ViT-e - a new 4B vision transformer they trained - and mT5-XXL respectively).
    - PaLI is then further pretrained on a mixture of tasks, such as image captioning, VQA, scene-text understanding, etc., where the tasks are framed as "image + query to answer" and where the query and answer are texts. They also include purely textual tasks in the pretraining to avoid the LLM's catastrophic forgetting of its NLU and NLG capabilities. All pretraining tasks use teacher forcing.

  - Dataset

    - WebLI consists of 10B pairs of web-scraped image-text examples, where the text is in 109 languages.
    - To mitigate test set leakage which would otherwise be present due to the size of the dataset, they perform near-deduplication of the images against the train, validation and test splits of 68 common vision-language datasets. The resulting dataset is only shrunk by 0.36%.
    - To improve data quality, they score the image-text pairs based on their cross-modal similarity score. This score is the cosine similarity between the embeddings of both modalities. These embeddings are computed as follows:
      - The image embeddings are trained first, using a semi-supervised approach.
      - Then the text embeddings are learned via a contrastive approach using a transformer encoder. This forces the embeddings of both modalities to be in the same multi-dimensional space.
    - To train PaLI, they only keep the 10% of the original WebLI pairs that score higher for cross-modal similarity (that is, approximately 1B examples).
   
  
- Results

  - The alargest PALI model (17B) achieves SOTA on various benchmarks in image captioning and VQA.
  
