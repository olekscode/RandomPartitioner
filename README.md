# RandomPartitioner

[![Build Status](https://travis-ci.org/PharoAI/RandomPartitioner.svg?branch=master)](https://travis-ci.org/PharoAI/RandomPartitioner)
[![Build status](https://ci.appveyor.com/api/projects/status/rvuim7w31nf8s0r9?svg=true)](https://ci.appveyor.com/project/olekscode/randompartitioner)
[![Coverage Status](https://coveralls.io/repos/github/PharoAI/RandomPartitioner/badge.svg?branch=master)](https://coveralls.io/github/PharoAI/RandomPartitioner?branch=master)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/PharoAI/RandomPartitioner/master/LICENSE)
[![Pharo version](https://img.shields.io/badge/Pharo-10-%23aac9ff.svg)](https://pharo.org/download)
[![Pharo version](https://img.shields.io/badge/Pharo-9.0-%23aac9ff.svg)](https://pharo.org/download)

This is a Pharo library for partitioning a collection. Given a set of K proportions, for example 50%, 30%, and 20%, it shuffles the collection and divides it into K non-empty subsets in such a way that every element is included in exactly one subset.

`RandomPartitioner` can be used in machine learning and statistical analysis for splitting the data into training, validation, and test (a.k.a. holdout) sets, or partitioning the data for cross-validation.

## How to install it?

To install `RandomPartitioner`, go to the Playground (Ctrl+OW) in your [Pharo](https://pharo.org/) image and execute the following Metacello script (select it and press Do-it button or Ctrl+D):

```Smalltalk
Metacello new
  baseline: 'RandomPartitioner';
  repository: 'github://pharo-ai/RandomPartitioner/src';
  load.
```

## How to depend on it?

If you want to add a dependency on `RandomPartitioner` to your project, include the following lines into your baseline method:

```Smalltalk
spec
  baseline: 'RandomPartitioner'
  with: [ spec repository: 'github://pharo-ai/RandomPartitioner/src' ].
```

If you are new to baselines and Metacello, check out this wonderful [Baselines](https://github.com/pharo-open-documentation/pharo-wiki/blob/master/General/Baselines.md) tutorial on Pharo Wiki.

## How to use it?

### Simple example

Here is a small array of 10 letters:

```Smalltalk
letters := #(a b c d e f g h i j).
```
We can split it in 3 random subsets with 50%, 30%, and 20% of data respectively:

```Smalltalk
partitioner := RandomPartitioner new.
subsets := partitioner split: letters withProportions: #(0.5 0.3 0.2).
```
The result might look something like this:

```
#((d h j a b)
  (i f e)
  (g c))
```

Alternatively, you might want to specify exact sizes of each partition. Let's split the array in two random subset with 3 and 7 elements:

```Smalltalk
subsets := partitioner split: letters withSizes: #(3 7).
```

This may produce the following partition:

```
#((d e a) 
  (c j g f i b h))
```

### Practical example: training, validation, and test sets

In this example, we will be splitting a real dataset into three subsets: one for training the machine learning model, one for validation (adjusting the parameters of the model) and one for testing the final result (a separate subset of data that is not used during training and allows us to evaluate how well does the model generalize by feeding it with previously unseen data).

We will be working with [Iris dataset](https://en.wikipedia.org/wiki/Iris_flower_data_set) of flowers - it is a simple and relatively small dataset that is widely used for teaching classification algorithms.

The easiest way to quickly load Iris dataset is to install the [Pharo Datasets](https://github.com/PharoAI/Datasets) - a simple library that allows you to load various toy datasets. We install it by executing the following Metacello script:

```Smalltalk
Metacello new
  baseline: 'Datasets';
  repository: 'github://PharoAI/Datasets';
  load.
```

Now we can load Iris dataset:

```Smalltalk
irisDataset := Datasets loadIris.
```

This gives us a [data frame](https://github.com/PolyMathOrg/DataFrame) with 150 rows and 5 columns. Just to ilustrate what we are working with, here are the first 5 rows of our dataset:

| sepal length (cm) | sepal width (cm) | petal length (cm) | petal width (cm) | class |
|-----|-----|-----|-----|--------|
| 5.1 | 3.5 | 1.4 | 0.2 | setosa |
| 4.9 | 3.0 | 1.4 | 0.2 | setosa |
| 4.7 | 3.2 | 1.3 | 0.2 | setosa |
| 4.6 | 3.1 | 1.5 | 0.2 | setosa |
| 5.0 | 3.6 | 1.4 | 0.2 | setosa |

We split this data frame into three non-intersecting subsets: we will use 50% of data for training the model (75 flowers), 25% of data for validating it (37 flowers), and 25% for testing (38 flowers).

```Smalltalk
partitioner := RandomPartitioner new.
subsets := partitioner split: irisDataset withProportions: #(0.5 0.25 0.25).

irisTraining := subsets first.
irisValidation := subsets second.
irisTest := subsets third.
```
