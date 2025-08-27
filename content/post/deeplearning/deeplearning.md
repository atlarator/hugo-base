---
title: "DL First Taste"
date: 2025-08-23T12:20:01+08:00
draft: false
tags: ["AIMLDL"]
---

## Lecture 1

neuron: a function that records a feature  
connection: a number of weight  
spread: calculate the function, times the weight, sum the bias and impulse using ReLU(modern way) or Sigmoid(older way)  

Deep Learning itself is just build a huge function.  

## Lecture 2

Cost: how the whole network recognizes your data  
It's always easy to find a local minimum in a function  
The way to calculate the gradient descent is backpropagation  

## Lecture 3

backpropagation: calculate which bunch of biases & weights change will have a repid decrease on cost function, all of the calculation rely on previous layer of network  
backpropagation needs a huge bunch of data to train the model, it will be good to divide random shuffled data into mini-batches, and do backpropagation one by one  

## Lecture 5

large language model: generate next possible word based on previous input(context of human-AI talking)  
train, reinforcement learning with human feedback  
transformers: attention, forward propagation, attention, so on  
finally choose the most value connects to the context to output  

## Lecture 6

Attention: vectors communicate with each other to change value  
propagation: vectors passes through network to another attention layer  
weights are ones to train, not data  

embedded layer: a huge matrix for words and their features  
unembedded layer: use the last vector to generate the probability of next possible word to generate  

softmax: an algorithm turns vectors into probability distribution, to let largest value close to 1 and smallest value close to 0  

## Lecture 7

query: what the word care about in the context  
key: what is provided for queries to check whether it is relevant  
value: final result that the word vector should care  
using softmax to form a normal data, before this, change all non-relevant words a neg-inf weight  
it would be hard to enlarge the context size  
multi-head is the way GPT scale-out  

## Lecture 8

perpendicular: same family of meaning, find most vectors that are perpendicular   
linear, ReLU, linear, MLP are just simplest neural networks  