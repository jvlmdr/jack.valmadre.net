---
layout: post
title:  Decisions in hard negative mining
---

It's common to use Hard Negative Mining (HNM) when training a classifier for sliding window detection.
Popular since the Dalal and Triggs paper of 2005, the concept is to train an initial detector using a set of random negative windows, then apply the detector to the training set and add false positives to the set of negative examples.
This can be repeated several times.

When the classifer used is an SVM, HNM can be interpreted as a technique to find the support vectors.
It is also a way to incorporate the effect of Non Maxima Suppression (NMS) into the training objective.
