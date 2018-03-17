---
title: "Deep Learning for Machine Translation"
description: Delivered in Fall 2017 at Heinrich-Heine-Universität Düsseldorf (DGfS-CL Fall School).
slug: teaching/2017-dgfs-cl
comments: true
---
[Hassan Sajjad](https://hsajjad.github.io) and I were fortunate enough to have the opportunity to
teach a deep learning course at the [Computation Linguistics school organized by _Deutsche Gesellschaft für Sprachwissenschaft_](http://cl-fallschool2017.phil.hhu.de). This course is geared towards students with a limited background in 
deep and machine learning. It walks them through building their very first machine learning model,
all the way up to developing a strong intuition behind sequence-to-sequence models, with the material
wrapped in the context of language. We also look at practical considerations when 
training these models, write code and perform exercises that mirror
what is learned in the lectures and finally peek into some techniques of better understanding what
these state-of-the-art models actually learn about language.

### The official spiel
> Statistical methods have dominated the field of machine translation for almost a decade now. These methods use a parallel corpus, i.e. a set of sentence pairs, where a sentence pair consists of a source language sentence, and its corresponding target language translation. The main objective of these methods has been to learn a mapping between the source and target words, and then use this mapping to generate translations of new source sentences. As recently as a couple of years ago, Deep Neural Networks have dethroned the Phrase based methods, and have been shown to give state-of-the-art results for machine translation.

> In this lecture series, we will first cover the basics of statistical machine translation to establish the intuition behind machine translation. We will then cover the basics of neural network models – word embedding and neural language model. Finally, we will learn an end-to-end translation system based completely on deep neural networks. In the last part of the lecture series, we will learn to peek into these neural systems and analyze what they learn about the intricacies of a language like morphology and syntax, without ever explicitly seeing these details in the training data. We will see how to adapt these models quickly to the required domain without retraining the model from scratch.

#### Pre-requisites
- Python
	- [Python/numpy](https://cs231n.github.io/python-numpy-tutorial/) tutorial
	- [iPython/Jupyter notebook](https://cs231n.github.io/ipython-tutorial/) tutorial
- Basics of linear algebra
	- [Linear Algebra for Machine Learning](http://www.cedar.buffalo.edu/~srihari/CSE574/Chap1/LinearAlgebra.pdf)

Small portions of the lectures and exercises are dedicated to refreshing these concepts as well!

#### Lecture materials
- **Lecture 0:** Introduction & Roadmap [[slides](/teaching/2017-dgfs-cl/Lecture0.pdf)]
- **Lecture 1:** Language & Translation [[slides](/teaching/2017-dgfs-cl/Lecture1.pdf)]
- **Lecture 2:** Language Modeling [[slides](/teaching/2017-dgfs-cl/Lecture2.pdf)]
	- Python tutorial [[iPython/Jupyter Notebook](https://drive.google.com/open?id=0BzdJvZytWnuzTnc5d1VWa3ItbUU)]
	- Python tutorial as a PDF [[non-editable](https://drive.google.com/open?id=0BzdJvZytWnuzMDRKbTNWWk96V3M)]
- **Lecture 3:** Machine Learning [[slides](/teaching/2017-dgfs-cl/Lecture3.pdf)]
	- Decision Boundary Exercise [[iPython/Jupyter Notebook](https://drive.google.com/open?id=0BzdJvZytWnuzU2RjOFFQUW5IQk0)]
	- Optimization functions demonstration [[iPython/Jupyter Notebook](https://drive.google.com/open?id=0BzdJvZytWnuzYUxKMGY2ZkluQ3c)]
- **Lecture 4:** Machine Learning II [[slides](/teaching/2017-dgfs-cl/Lecture4.pdf)]
	- Linear Classifier with MSE [[iPython/Jupyter Notebook](https://drive.google.com/open?id=0BzdJvZytWnuzTDVjdWJ2NFNxaTQ)]
	- Linear Classifier with Softmax and Cross Entropy [[iPython/Jupyter Notebook](https://drive.google.com/open?id=0BzdJvZytWnuzeVBDOWJ0ckhQQjg)]
- **Lecture 5:** Machine Learning and Neural Networks [[slides](/teaching/2017-dgfs-cl/Lecture5.pdf)]
	- Efficient Linear Classifier with Softmax and Cross Entropy [[iPython/Jupyter Notebook](https://drive.google.com/open?id=0BzdJvZytWnuzQVZtVmZsNTNqNE0)]
	- Neural Network [[iPython/Jupyter Notebook](https://drive.google.com/open?id=0BzwwwL3SyiJYVlAta1JhSk5pLXc)]
	- Neural Network with Keras toolkit [[iPython/Jupyter Notebook](https://drive.google.com/open?id=0BzdJvZytWnuzLU00MjZJQ2tXa1k)]
- **Lecture 6:** Neural Network Language Models [[slides](/teaching/2017-dgfs-cl/Lecture6.pdf)]
	- Language Modeling with Keras [[iPython/Jupyter Notebook](https://drive.google.com/open?id=0BzdJvZytWnuzMVE4d1o0YlVtbzA)]
- **Lecture 7:** Sequence to Sequence [[slides](/teaching/2017-dgfs-cl/Lecture7.pdf)]
- **Lecture 8:** Practical Neural MT [[slides](/teaching/2017-dgfs-cl/Lecture8.pdf)]
- **Lecture 9:** Analysis of Neural MT [[slides](/teaching/2017-dgfs-cl/Lecture9.pdf)]
- **Lecture 10:** Recent Advancements [[slides](/teaching/2017-dgfs-cl/Lecture10.pdf)]