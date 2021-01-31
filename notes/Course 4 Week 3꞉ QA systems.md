---
title: 'Course 4 Week 3: QA systems'
created: '2021-01-18T00:00:44.061Z'
modified: '2021-01-31T00:01:14.056Z'
---

# Course 4 Week 3: QA systems

- Given a question and context can you give an answer?
  - Context based question answering
    - take in question and context and you get the answer from the context. For example you pass in a question and some text and it returns highlighted parts of the text refering to that question.
  - Closed book: 
    - only takes the question and comes up with its own answer.
- Transfer learning for nlp applications
  - How to use transfer learning that includes how the model was trained and data as a way to improve the model on a new problem. Take already made model and fine tune on new data. Domains do not have to be the same neccesarily. Ideally this is done to reduce training time and might need less data.
- BERT
  - bidirectional context, everything before and after a token for example
- T5
  - single task vs multi task. T5 is one large model that takes in multiple tasks and outputs different outputs based on the task. More data you have generally means better performance, though not always.

## Transfer Learning
### Overview

When training what do we want? 
- Reduce training time
- Improve predictions
- Try to use less data

So how does transfer learning work? we have our standard model training that takes data, trains a model and outputs a prediction. First approach for transfer learning would be feature based transfers such as word vectors and second is finetuning which takes an already made model and trains it some more on a new task. 

Pretrained data can be labled or unlabled. pre-training tasks would be like language modeling, masked words, next sentence.

### Transfer learning methods:

General purpose learning suppose we have a bag of words model that says "I am BLANK beacause I am learning" and we want to learn that BLANK is "happy". Word embeddings could be used to help learn these vectors and can be applied to multiple other domains.

Feature based vs Fine-tuning

Featurebased just means using the featurs as input to another model for the output, such as learning embeddings from text, and take the learned weights as input to a new model that you can train and get new predictions on.

Fine-tuning based means you train a model then use that exact model to as well train on a new task. With fine tuning you can add additional layers to the model to match the new problem. 

Pre-train data
labeled vs unlabeled

unlabeled: pretraining with no labels. Just train model on the data. then take that trained model weights and try to predict with data that has labels. Somewhat self supervised learning. For example randomly masking words in a data set to predict the word would be a self supervised learning task. Input into the model the sentence with the missing word, put it through the language model, make a prediction then check with the unlabeled data what the correct word should be. THen can use that to update the model based on how well the prediction was.

## Overview of GPT, ELMO, BERT T5

Say we have a word "right". We want to know the context of this word to generate features. For CBOW we use a fixed window to learn the features. To take this further ELMO used an RNN that learned the full context from beginning to end to learn the term context in relation to where it is in the sequence. GPT was unidirectional and a decoder. BERT is transformer encoder model, bidirectional, multi-mask, next sentence prediction. T5 encoder decoder transformer model. Bidirectional and multi-task.

so CBOW had limited context, ELMO had more context but since it was a bidirectional RNN it had limits with long sequences. Then GPT came along and was only unidirectional BUT it was a transfomer decoder model. BERT then came along and took the transformer and made it bidirectional with the encoder and then T5 which makes use of transferlearning and uses teh same model to predict on many tasks by using encoder decoder archietecture.

## BERT Outline

- What is the architecture?
  - Uses transfer learning and pre-training
    - Start with some input embedded -> transformer blocks -> output
  - pre-training model on unlabled data on different pre training tasks
  - multi layer bidirectional transformers
  - positional embeddings
  - bert_base: 110 million parameters 12 attention heads and 12 layers
- How BERT pretraining works.
  - Start by masking 15% of the words in the dataset at random
  - If the ith token then 80% of the time replace with the masked token, 10% of the time with random token, and 10% unchanged ith token.
  - uses cross entropy loss to learn the missing token
  - KNown as a masked language model (MLM)
  - there could be multiple masked spans in a sentence

## BERT objective

- Understand how inputs are fed in
  - Position Emb.
    - Positional information for the tokens
  - Segment Emb.
    - Segements each sentence into their own embeddings
  - Token Emb.
    - Token embeddings of the sequence with a [CLS] token at the start of each sentnece and [SEP] token at the end of each sentenc
  - Each of the above embeddings are then sum them together to get the final embedding matrix for the input
- Visualize the output
  - See video for diagram
- What is the BERT objective?
  - Object 1: Multi-Mask LM to predict the word that is being masked
    - Loss: Cross Entropy
  - Object 2: Next Sentence Prediction
    - Loss: Binary loss function
  - then the two objectives are then added together to get the final loss

## How to Fine-tune BERT

For BERT since it expect sentence A and sentence B as input you just replace the sentences with the information for the task you want so for question answering it would be (Question, Answer) or for say NER can have (Sentence, Entities) etc. Basically so log as the inputs follow the (A, B) format.

## T5 Model

Uses mixed of transfer learning and Masked Language modeling. It is known as the Text to Text transformer since it performs tasks based on the input task text. It works by taking an input sentence say "I like the dog" and then masks are put over certain words, think its random, and then you replace them with "\<X\>" "\<Y\>" for x = like and y = dog. Then you set the targets to be the words that are masked and mask out the previously unmasked words. So input "I \<X\> the \<Y\>" and target becomes "\<X\> like \<Y\> dog"

## Multi Task Training

- 
