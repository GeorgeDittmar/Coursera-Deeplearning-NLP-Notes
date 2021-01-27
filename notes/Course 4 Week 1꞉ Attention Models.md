---
title: 'Course 4 Week 1: Attention Models'
created: '2020-12-29T22:06:00.084Z'
modified: '2021-01-18T06:01:51.347Z'
---

# Course 4 Week 1: Attention Models

We will go over finetuning pretrained language models and others which is more likely for many researchers to use unless you work at google.

## Seq2Seq models
- Neural machine translation
  - use encoder adn decoder to translate from english to another language
  - introduced by google in 2014. Maps variable length sequences to fixed-lenght memory
  - LSTM and GRU typically used to handle vanishing gradients
  - Consists of an encoder then hidden states to then go into a decoder

- Seq2Se2 model and its shortcomings
  - there is an information bottleneck inside the model so long sequences become problematic as there are more and more inputs that the decoder must predict. The issue is that later input steps can be given more importance which might not be correct.
  - thus as sequence size increases model performance decreases due to the bottleneck due to the fixed length memory.
- Solution for the information bottleneck
  - to help with this problem if you can get the model to focus "attention" on the right place, you can make the model have a way to focus on the likeliest words at each step
  - this is done by providing the information specific to each input word

## Alignment

The idea is to align the words of the input with the words of the output.  Some examples are:
- Translating from one language to another
- Words sense discovery and disambiguation
- This week we will want to achieve alignment with a ssytem from retrieving information step by step and scoring it

Word alignment

Words do not always line up when translating. For example the Ich bin ein berliner. This is where the attention layers comes in to add some additional weights to the inputs. When the layer adds a strong weight to an incoming term, the decoder will be strongly influenced on its output.

Calculating the alignment:

Get all hidden states ready for the encoder and decoder. Score each encoder hidden state. If one score is higher than the other then that hidden state will have more influence than others. then run the score through softmax to get the attention distribution. take the alignment score and multiply it by the encoder hidden state then add it up to be the context vector and send that to the decoder.

## Attention

- Concept of attention for IR
  - Its like trying to find your keys. You weigh up the possibilities of where they might be and tells you the most likely locations.
- Keys, Queries, and Values
  - uses encoded reps of input and output. Keys and values are pairs of dimension N where N is the input sequence length. They come from the encoder hidden states. The query comes from the decoder hidden states. both the kv pair and the query are sent to the attention layer and the dot product of the query and key is calculated.

  Can think of it as a matrix of key and query where the diag is the strongest corelation between one term and another.

  recap:

  - Attention is an added layer that helps models to focus on whats important
  - Q, V, K are used for information retrieval inside the attention layer by calculating the similarities between the decoder queries and the encoder key value pairs. (where does value actualyl get used though?)
  - Attention can help find matches between languages that even have different gramatical structures.

  Keys and values have their own matrices but they are of the same shape (Why?). 
  
Ex. Translating English to German.

The word embeddings in the english language as keys and values. The queries would be the German equivalanets. You then take the dot product of Q and K which would help identify terms that are similar to the keys which would make the model focus on the right translationand then run a softmax over that result. We then multiply that by V which in this example is the same as the keys.

## Setup for machine translation

- data usually in sequence pairs
- dataset will not be entirely clean
- state of the art uses pre trained vectors while others use one-hot vector to represent the words
- keep track of mappings with word2index and index2word dictionaries
- also use start and end tokens
- padd the input as well

## Training NMT with Attention

How to know predictions are correct?

- Teacher Forcing
  - Allows model to check its look at each step or compare the prediction against the real output during training
  - Faster training and better accuracy
- Model for NMT with attetion
  - pre-attention decoder is transforming prediction targets into the query vector. the vector is shifted one place to the the right so that teacher forcing can take place. That means when we predict the correct target word can just be fed into it.
  - the encoder is being input the tokens to generate the key and value

## Eval Teacher Forcing

BLEU score: Bilingual Evaluation Understudy
- evaluates the quality of machine translated text by comparing candidate text to one or more reference translations. Range is between 0 and 1 with 1 being best
- We start with our set of candidate words and we see how many of them appear in the reference translations.
  - for example from video the words I and am appear in the reference translation and there are a total of 5 candidate words so BLUE score is 2/5
  - Works but doesnt take into account semantic meaning or structure, it is just a straight count

ROUGUE: Recall Oriented Understudy for Gisting Evaluation
- Recall focued so places more importants on how much of the human created reference is in the machine translation
- Measures precision and recall between machine and human translated text
  - looks at ngram overlap between machine translated text and reference ( sum of unigram overlap in model and reference) / total words in reference ( recall)
  - precision: (sum of unigrams overlapped in model and reference) / number of words in model
- doesnt take themes or concepts into consideration to low score doesnt mean the translation is bad

## Sampling and Decoding
How do we best decide how to decode the next token from a list of possibilities?
- Greedy Decoding
  - selecting the next most probable token in the sequnces BUT may not be best idea for long sequences
- Random Sampling
  - Often little too random but takes probabilities for next possible token and samples based on that
- Temperature: parameter that allows for more or less randomness in the sampling predictions. scale 0 to 1
  - lower temp == more conservative / safe choice
  - higher temp == more random 

- Beam Search: broader more exploratory alternative
  - slelects multiple outputs based on conditional probability
  - number of options depends on how many beams "B"
  - Select B number of best alternatives at each time step
  - Then select the one with the highest probability from the beam
  - Beam search uses probability distributions so might not learn a distribution that is not accurate in reality
- Minimum Bayes Risk (MBR)
  - compares many samples against one another. To implement
    - Generate several random samples
    - Compare each sample against all the others and assign a similarity score (ROUGE or something)
    - Select sample with highest similarity

  - Example: MBR sampling on 4 samples
    1. Calculate similatirity score between sample 1 and 2
    2. Calcualte similarity score between sample 1 and 3
    3. Calculate similarity score between sample 1 and 4
    4. Take the average (usually weighted average) of the first 3 steps
    5. Repeat until all samples have overall scores
    6. Finally highest score will be chosen
Summary
  - Beam search uses conditional probabilities and the beam width parameter
  - MBR takes several samples and compares them against each other to find the "golden one"


