# Paper

- Title: ANNO-MI: A Dataset of Expert-Annotated Counselling Dialogues
- Authors: Zixiu Wu, Simone Balloccu, Vivek Kumar, Rim Helaoui, Ehud Reiter, Diego Reforgiato Recupero, Daniele Riboni
- Link: https://ieeexplore.ieee.org/document/9746035
- Repository: https://github.com/uccollab/annomi
- Tags: Datasets, Counselling, Psychotherapy, Chatbots
- Year: 2022

# Summary

- What
  
   - A dataset of 133 professionally transcribed and expert-annotated Motivational Interviewing dialogues.
   - Motivational Interviewing (MI) is a therapeutic framework developed to elicit positive change in the patient.
  
  
- Details
  
  - The conversations were transcribed from MI demostration videos available online. All conversations are complete.
  - The conversations are labelled as high- or low-quality MI. The authors observe that in high-quality sessions the therapist centres on the patient and is empathetic, whereas in low-quality ones he/she just provides instructions/suggestions. The dataset contains 110 high-quality conversation and 23 low quality ones.
  - Each utterance in the conversations is individually annotated by MI practitioners:
    - Therapist utterances are annotated with the labels Question (when the therapist asks the patient something), Input (when the therapist provides information, advice, options or sets goals), Reflection (when the therapist listens to the patient and indicates they understand them) or Other (when none of the above behaviour is displayed). When more than one behaviour is present, the utterance is labelled with the main one.
    - Client utterances are annotated with the labels Change (indicating willingness to alter their behaviour), Sustain (indicating resistance to change) and Neutral.