# Paper

- Title: Towards Emotional Support Dialog Systems
- Authors: Siyang Liu, Chujie Zheng, Orianna Demasi, Sahand Sabour, Yu Li, Zhou Yu, Yong Jiang, Minlie Huang
- Link: https://arxiv.org/abs/2106.01144
- Repository: https://github.com/thu-coai/Emotional-Support-Conversation
- Tags: Datasets, Emotional Support, ESC, chatbots
- Year: 2021

# Summary

- What
  
  - They define the Emotional Support Conversation (ESC) task
  - They introduce a ESC framework
  - They present ESConv, an Emotional Support Conversation dataset

- How

  - Task definition
    - A user who is experiencing a negative emotional state is assigned an emotion label $e$ and an integer emotion intensity level $l$ ranging from 1 to 5.
    - The user is also assigned the underlying challenge that they are going through.
    - The supporting system's task is to comfort the user in a conversational setting and lower the intensity level of the negative emotion.
    - Therefore, during the conversation, the supporting system needs to indentify the underlying issue, comfort the user and provide suggestions to help the user cope.
    - The effectiveness of the system is measured in terms of the decrease in intensity level of the user's negative emotion by the end of the conversation.
    - Moreover, the capability of the system to effectively identify the underliying problem, comfort the user and provide solutions/suggestions is measured.
    - We can thus identify three subproblems in the ESC task:
      1) Support strategy selection and strategy-constrained response generation. The authors note that the timing of applying strategies is relevant to the effectiveness of ES.
      2) Emotion state modeling, as the user's emotional state must be tracked dynamically (this is used both in strategy selecion and for evaluation).
      3) Evaluation of ES effectiveness, which is in addition of measuring other conversational metrics such as relevance, coherence and engagement.

    <img src="https://github.com/lisaalaz/papers/blob/master/images/ESC_conversation.png" width="400">

  - ESC Framework
    - The ESC Framework (based on the Helping Skills Theory) consists of three stages: Exploration n (exploring to help the help-seeker identify the problems), Comforting (providing support through empathy and understanding) and Action (helping the help-seeker make decisions on actions to cope with the problems).
    - Note that in the original Helping Skills Theory the second phase is Insight (helping the help-seeker move to new depths of selfunderstanding), here adapted to Comforting since - according to the authors - the former *usually requires re-interpreting users’ behaviors and feelings, which is both difficult and risky for the supporters without sufficient support experience*.
    - Each of these three stages contains several support strategies, adapted from the Helping Skills Theory (again, note that some strategies in the original literature have been discarded by the authors, such as the challenging strategy which requires pointing out the discrepancies or irrational beliefs that the helpseeker is unaware of or unwilling to change).

     ![image](https://github.com/lisaalaz/papers/assets/89645136/e6c146cb-17c2-4256-8f33-e88883c7abc4)
    
  - Dataset   
    - They collect the ESConv dataset by having trained crowdworkers chatting in pairs and playing help-seeker and supporter roles.
    - Supporters rate the extent that the help-seeker goes into detail about their problems after the conversation, using a five-point Likert scale.
    - Help-seekers complete a pre-chat survey identifying one problem from 5 options and one emotion from 7 options, also giving an intensity to this emotion on a discrete 1-5 scale (where 5 is most intense). They also describe the cause(s) of the problem in an open-text field, and identify whether this is something they're currently going through or it is based on previous experience (75.2% of conversations in the dataset are current experience).
    - During the conversation the help-seekers also give feedback every 2 supporter's utterances, scoring their helpfulnes on a 5-star scale. The scores obtained overall are always above 4 stars.
    - Help-seekers also rate their emotion intensity after the conversation, as well as the supporter's empathy and understanding and the relevance of their responses. 
    - Collected conversations that are evaluated to be low-quality are filtered out.

    <img src="https://github.com/lisaalaz/papers/blob/master/images/ESC_dataset.png" width="500">
    
    <img src="https://github.com/lisaalaz/papers/blob/master/images/ESC_strategies.png" width="500">

- Experiments

  They experimented with several models based on BlenderBot small and DialoGPT small. These models variants are:
  1) Vanilla: the above models without any changes, finetuned on ESConv with no access to strategy annotations.
  2) With strategy: they prepended to each utterance in the train set a unique token corresponding to the corresponding strategy. Then, taking the dialogue context as input, the model generates the next utterance conditioned on a strategy token. For this they used three methods:
     - Oracle (response is generated conditioned on the gold reference strategy token)
     - Joint (response is generated conditioned on the prefdcted strategy token)
     - Random (response is generated conditioned on a randomly selected strategy token)

  Automatic evaluation:

  They evaluated all the variants and methods above on various automated metrics. Thes results show that: 
  - Oracle models are superior to vanilla models, but joint models perform worse than vanilla if the predicted strategy is different from the ground truth.
  - BlenderBot-based variants perform better than DialogGPT-based ones.
  
  <img src="https://github.com/lisaalaz/papers/blob/master/images/ESC_aut_eval.png" width="500">

  Human evaluation:
  
  They focussed the human evaluation on the BlenderBot-based variants only. For this, they had crowdworkers chat with the models as if they were the help-seekers, whereas the model acted as the supporter. Each crowdworker chatted with two model variants and was asked to evaluate:
  1) fluency
  2) identification (which model was more helpful in indentifying problems)
  3) comforting
  4) suggestion (which model gave the most helpful suggestions) 
  5) and each crowdworker also expressed their overall preference between the two models.

  Each pair of models was compared by 100 conversations with crowdworkers.

   <img src="https://github.com/lisaalaz/papers/blob/master/images/ESC_human_eval.png" width="500">
   
   <img src="https://github.com/lisaalaz/papers/blob/master/images/ESC_strategies_human_eval.png" width="500">
