---
title: "Forged Signature Detection: A deep learning Approach"
date: 2018-09-24T11:07:10+06:00
author: [ "Chirag Mehta" ]
image : "images/blog/blog-post-2.png"
bg_image: "images/feature-bg.jpg"
categories: ["Artificial Intelligence"]
tags: ["Deep Learning","Pytorch","Computer Vision","Siamese Networks","Signature verification"]
description: ""
draft: false
type: "post"
---


Signature verification is an essential task in Computer Vision. The approach to solving the problem can vary based on the kind of problem we have. In this article, we will try to solve the offline signature verification problem and would use a writer-independent approach. </p>

## **Online vs Offline**

To capture signatures online, you would need an electronic device that can record a sequence of coordinates, writing speed, and pressure. These would be used as additional information in the verification process. However, it is not always possible to have such a device around. For example, with cheques, you cannot get such additional information. With such an amount of money being involved with the banking industry, offline signature verification becomes a rather crucial problem.</p>

## **Writer Dependent vs Writer Independent**
As the names suggest, in writer dependent approach a model is trained for every writer, while in the case of writer independent, the model is trained independently of the writer. Writer dependent system needs to be updated with every new writer.

We need to somehow measure some sort of distance between signatures. The distance should be high between a true signature and a forged one, while it should be small between two true signatures. The formulation directs us to siamese networks.

## **Siamese Network**

Siamese network (twin neural network) is often described as having two networks that are identical. The structure, the weights, and the updates of weights are all the same. The input is passed through the network to get an embedding vector which is what we would use to calculate the “distance” between two inputs. I’m not particularly fond of this definition, the way I like to think of it is just one network, which gives you an output layer vector, and that is what you use to find the “distance” between two inputs.</p>

![SiameseNetwork](/images/blog/blog-post-2_1.png)
source: https://pyimagesearch.com/2020/11/30/siamese-networks-with-keras-tensorflow-and-deep-learning/
## **Loss Function**

Siamese network is often trained with triplet or contrastive loss. In this article, I will use contrastive loss because that’s what SigNet uses.
### Contrastive Loss

![FinalResult](/images/blog/blog-post-2_2.png)

Here, s1 and s2 are the two samples, y is a binary indicator of whether the two samples belong to the same class or not, y=0 if the samples belong to the same class. alpha and beta are two fixed constants, m is the margin. The signet paper uses m=1. In this article, I’ll use m=10. Dw is the euclidian distance computed in the embedding feature space.

From the SigNet paper</p>
image4</p>

> Siamese network aims to bring the output feature vectors closer for input pairs that are labelled as similar, and push the feature vectors away if the input pairs are dissimilar.

## **The Code**

The dataset used for this code is CEDAR.

We will start by writing down our network and defining the loss, I’ve written a very simple network, the aim of the code is to just demonstrate signature verification and not to achieve good results.

Instead of pooling layers, I’ve used stride=2 to downsample.

Importing the essential libraries

Next, we need to fetch all the images from the directory in which they are present. Then we need to resize them and convert to tensor

Next, we need to create a Dataloader. My Dataloader is rather ugly. We need to give a tuple of two images and target to the network for training. We would also want to give enough true and forged samples to the network to train properly. I randomly choose the images while trying to maintain an equal probability of giving true and different (forged+totally different signatures). This slows the program and also might cause the network to see some samples more often than others. In the future, I’ll try to come up with a better Dataloader.

Now, we just have to initialize the network and select an optimizer. I’ve used the good old ADAM optimizer.

Now the training

In order to decide if two images belong to the same class or a dissimilar class, we need to set a threshold value on the distance, or one could use the sigmoid function and then set a threshold on the “confidence”. </p>

![FinalResult](/images/blog/blog-post-2.png)

Here I calculate the distance between the set of images given above</p>
> The results obtained  
> tensor([0.1780])  
> tensor([12.5088])  
> tensor([13.8891])
