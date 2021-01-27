---
title: Siamese Networks NLP
created: '2020-12-06T19:54:39.918Z'
modified: '2020-12-09T07:20:50.970Z'
---

# Siamese Networks NLP

Can be used to find duplicate questions or other duplicates. Siamese networks try to find the difference or similarity of two examples. in cv they are used to compare similarity of objects or images.

basic architecutre is using two networks in parallel and them compare the outputs via a cosine similarity score. Each network share the same parameters so you only need to train one set of weights.

Loss function: Compare questions to an "anchor" question and see if it is positive or negative, not in sentament sense.

Anchor: How old are you?
example: What is your age? (positive example compared to anchor)
example: Where are you from? (negative example compared to anchor)

Loss function will generate a value between -1 an 1. The loss function will be similarity(A, N) - similarity(A, P).

## Triplets
Way to set up the data so you can tell the difference between two inputs. Its basically just a tuple with 3 elements, the anchor, the positive, and the negative examples. You then use the triplet to learn the similarities. The idea is to use these triplets to minimize the difference between the similarities of the inputs to the anchor. The loss then is zero if the diff is less than or equal to zero or the diff so we dont accidentally unlearn what we just did due to negative numbers. Can add an alpha hyper param to help tune the model on when to say something is zero or shift it a little in any direction.

When building triplets, harder ones are usually better. creatig triplets at random will most likely just make a model thats easier to learn but not have much robustness. Making triplets that have "tougher" examples will take longer to train but usually gain better information for the model.

## Computing the cost

Start with creating batches of size "b" pairs of duplicate examples that are not duplicates of other examples in the batch. You then run the data through the siamese network and build an output matrix for each question pair run through network 1 and 2. This will create a matrix of similarities between all pair combinations.

Hard negative mining. Can take the mean negative, the off diagonal row values. Also choose the closest negative to the diagonal value

Triplet loss function:

$$
L_{original} = max(s(A,N) -s(A,P),0)
$$

Then can improved by adding the mean negative and adding the closest negative to create a new loss function

$$
L_1 = max(mean_neg-s(A,P)+\alpha, 0)
$$
$$
L_2 = max(closest_neg-s(A,P)+\alpha,0)
$$
$$
L_{full} = L_1 + L_2
$$

## One shot learning
Good way to compare things to a base when your data could be changing and dont want to retrain a whole model. THe idea is to train a similarity metric instead of trying to train a classifier to see what class does this example fall into.
