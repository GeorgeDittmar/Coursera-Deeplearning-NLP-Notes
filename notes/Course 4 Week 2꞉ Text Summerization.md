---
title: 'Course 4 Week 2: Text Summerization'
created: '2021-01-08T05:06:12.684Z'
modified: '2021-01-11T00:55:19.673Z'
---

# Course 4 Week 2: Text Summerization
## Transformer vs RNN

* Issues with RNN architectures
  * No parallel computation with RNN since you have to compute each step one at a time through the sequence
  * Potential for vanishing gradients / information loss the longer the sequences become
* Comparison with Transformers
  * Transformers do not need to run over the data sequencially since they are based on attention which only is a single step over the sequence
  * Dont suffer from vanishing gradient problems
  * Gradient step taken from output to input is also just one while RNN its equal to the number of timesteps in the sequence
  * Architecture allows for parallelisation of the processing
* Multi headed attention
* Position Encoding
  * Encoding that maintanes positional information by outputting values that are added to the embeddings

## Applications of transformers
* Transformers in application
  * Text summerizatoin
  * Auto complete
  * chatbots
  * NER
  * QA
  * etc
* Some Transformer model types
  * GPT2
  * BERT
  * T5
* Introduction to T5
  * single model that can leard to do multiple tasks
    * Give input string that defines what you want the model to do and the string to do it on ( look up more on how they did that)

## Dot Product Attention
* Consider translating english to german
  * A query ( german word) looks for similar keys (english words). The attention layer takes the queries (german word vectors) and they are matched against all keys and given probabilities to how close that query matches the key. Each key has a probability of being a match for the query. Then return a sum of th ekeys weighted by their probabilities.

  * Need to define the 3 main matrices Queries Keys and Values
    * for exmample " I am Happy". Send that to an input embedding and then we have 3 linear layers Q, K, V that are used to build the attention matrices for all the words. 

  So for example "I" gets an embedding and is passed through the linear layers to generate Q, K, V. This is done for "am" and "happy" so we get the final matrix of Q,K,V of all the words. Q and K are then multiplied together then a softmax is taken on that and multiplied with the V matrix.

  $$
  softmax(QK^T)\times V
  $$

  The attention weights can be viewed as allignment scores. When you multiply the softmax by the V matrix you get probabilites and these probabilities are used as the attention result.

  One thing thats confusing is that often Keys and Values are the same (Why?). Keys can be viewed as the embeddings of English words and Queries as the embeddings of german words. Then you take the dot product of Q [L_q, D] and K [L_k, D] and pass that to a softmax since the dot product doesnt add up to 1 always and this gives the attention weights between the queries and the keys. After softmax the weights have shape [L_Q, L_K] and are then multiplied by the value matrix to turn them into probabilities. query q_i gets v_j from the most similar k_j.

  Attention formula:

  $$
  Z = attention(Q,K,V) = softmax(QK^T)V = W_{A}V 
  $$

  This means that attention can basically be done with just 2 matrix multiplications! 
  
* Dot product attention is essential for the Transformer
* The input to attention are Q, K, V
* A softmax function makes attention more focused on the best keys
* GPU's and TPU's are great for the matrxi multiplication required

## Causal Attention

* Ways of Attention
  Three ways of attention:
    * Encoder/decoder attention: One sentence (decoder) looks at another one (encoder). Translation models use this.
    * Causal (self) attention: in one sentences words look at previous words for generation.
    * Bi-directional self attention: In one sentence, words look at both previous and future words.
* Overview of causal attention
  Qeuries and keys come from the same sentence so it is "self attention". Queries should only be allowed to look at words before the current word
* Math
  Similar to dot attention in that Q and K transpose are multiplied together to form the dot product but then they are added to a mask matrix called the constants matrix (M) which have zeros on and below the diagonal and huge negative numbers above the diagonal.

  $$
  W_A = softmax(QK^T + M)
  $$

  Adding the mask makes it so you can only check queries in the past. The final formula is very similar to the one previously.

  ## Multi-headed attention

  * Intuition
    * Head attention head uses different linear transformations to represent the words
    * Each head can learn different relationships as well
    * The input to a multi head attention is a triple if QKV still which is then fed into a fully connected dense linear layer to set them up for each attention head
    * Then split each vector into the number of heads and perform attention on them then concatenate them back up after and put them through a final fully connected layer.

  * Scaled Dot product and concatenation
    * You now take Q times K transpose over square root of d_k where d_k is the query and key dimension. The normalization is done so that the gradient is prevented from being extremely small when large values of d_k are used.
    * Concatenation:
      * input(Q,K,V): shape(batch, length, d_model)
      * linear layer: shape(batch, length, n_heads * d_heads) where it works on the final dimension and n_heads is the number of parallel heads and d_heads are the dimensionality of each head. This layer creates a set of linear layers each of size d_head
      * transpose: shape(batch, n_heads, length, d_head) apply attention by transposing all heads just after the batch dimension. Seperate heads dont interact with each other so it can be thought of like a batch. You then just apply dot product attention as normal
      * result : shape(batch, n_heads, lenght, d_head) result is reshaped and concatenated
      * transpose: shape(batch, length, n_heads * d_head): transposed back to get a matrix
      * linear layer: shape(batch, length, d_model) final dense layer mixes the heads into a join representation
  * Math 
    * Create initial embeddings of the inputs
    * then calculate QKV into their associated matrices. You create a matrix for each head so you create H QKV matrices
    * train weight matrices, one per head, to create new weighted QKV matrices
    * then create Z by taking the QKV matrices and the weights
    * then you get the output weights after the concactenation

$$
MultiHead(Q, K, V) = Concat(h_1,...,h_h)W^0 \: where\: h_i=Attention(QW_{i}^{Q}, KW_{i}^{K}, VW_{i}^V)
$$

(ADD DIAGRAM IMAGE HERE)
