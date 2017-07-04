# Tensorflow MNIST Convolutional Network Tutorial

This project is another tutorial for teaching you Artificial Neural Networks. 
I hope that my way of presenting the material will help you in long learning process. 
All the examples are presented in [TensorFlow](https://www.tensorflow.org/) and as a runtime environment 
I choose the [online python IDE - PLON.io](https://plon.io). PLON makes much easier to share this tutorial 
with you and run the computations online without any configuraion.

Project presents four different neural nets for [MNIST](http://yann.lecun.com/exdb/mnist/) digit classification. 
The former two are [fully connected neural networks](https://en.wikipedia.org/wiki/Artificial_neural_network) and latter are [convolutional networks](https://en.wikipedia.org/wiki/Convolutional_neural_network). 
Each network is build on top of previous example with gradually increasing difficulty in order to learn more powerful models.


Project was implemented in ***Python 2*** and Tensorflow v.1.0.0 but it is rather streightforward to run 
it in ***Python 3***


## Tensorflow neural network examples

* simple single layer neural network (one fully-connected layer), 
* 5 layer Fully-connected neural network (5 FC NN) in 3 variants
* convolutional neural netowork: 3x convNet+1FC+output - activation function sigmoid
* convolutional neural netowork with dropout, relu, and better weight initialization: 3x convNet+1FC+output 


## Single layer neural network

This is the simplest architecture that we will consider. This feedforward neural network will be our baseline model for further more powerfull solutions.
We start with simple model in order to lay Tensorflow foundations: 

* How to [work with placeholders](https://www.tensorflow.org/versions/r0.11/api_docs/python/io_ops/placeholders)?
* What [tensorflow varibales](https://www.tensorflow.org/api_docs/python/tf/Variable) are? 
* [Understanding Tensorflow shapes and dimensions](https://blog.metaflow.fr/shapes-and-dynamic-dimensions-in-tensorflow-7b1fe79be363).
* Initalize TF session
* Run computations in a loop.

File: **minist\_1.0\_single\_layer\_nn.py**


### Network architecture

This is simple one layer feedforward network with one input layer and one output layer

* Input layer 28*28= 784, 
* Output 10 dim vector (10 digits, one-hot encoding)

```
input layer             - X[batch, 784]
Fully connected         - W[784,10] + b[10]
One-hot encoded labels  - Y[batch, 10]
```

#### Model 

```
Y = softmax(X*W+b)
Matrix mul: X*W - [batch,784]x[784,10] -> [batch,10]
```

Training consists in finding good W elements,  this is handled automaticaly by Tensorflow Gradient Descent optimizer.


### Results

This simple model achieves **0.9237** accuracy

![Tensorflow MNIST train/test loss and accuracy for one-layer neural network](https://plon.io/files/58e3bfaf1b12ce00012bd731)



## Five layers fully-connected neural network

This is upgraded version of previous model, between input and output we added five fully connected hidden layers. Adding more layers makes network more expressive but in the same time harder to train. The three new problems could emerge: vanising gradients, model overfitting and computation time complexity. In our case where the dataset is rather small, we did not see those problems in real scale.

In order to deal with those problems, different training techniques was invented. Changeing from sigmoid to relu activation function will prevent vanising gradients, chosing Adam optimizer will speed up  optimization and in the same time shorten training time, adding dropout will help with overfitting.

This model was implemented in three variants, where each successive variant builds on previous one and add some new fatures:

* Variant 1 is simple fully connected network with sigmoid activation fucntion and Gradient descent optimizer
* Variant 2 use more powerful RELU acitivation function instead sigmoid and utilize better Adam optimizer
* Variant 2 add [dropout](https://www.cs.toronto.edu/~hinton/absps/JMLRdropout.pdf) usage in order to prevent overfitting



### Network architecture

All variants share the same network architecture, all have five layers with sizes given below:

```
input layer             - X[batch, 784]
1 layer                 - W1[784, 200] + b1[200]
                          Y1[batch, 200] 
2 layer                 - W2[200, 100] + b2[100]
                          Y2[batch, 200] 
3 layer                 - W3[100, 60]  + b3[60]
                          Y3[batch, 200] 
4 layer                 - W4[60, 30]   + b4[30]
                          Y4[batch, 30] 
5 layer                 - W5[30, 10]   + b5[10]
One-hot encoded labels    Y5[batch, 10]

model
Y = softmax(X*W+b)
Matrix mul: X*W - [batch,784]x[784,10] -> [batch,10]
```

### Results

All results are for 5k iteration.

* five layer fully-connected : **accuracy=0.9541**
* five layer fully-connected with relu activation function and Adam optmizer: **accuracy=0.9817**
* five layer fully-connected with relu activation, Adam optmizer and dropout: **accuracy=0.9761**


![Tensorflow MNIST train/test loss and accuracy for 5 layer fully connected network](https://plon.io/files/58e409241b12ce00012bd733)

![Tensorflow MNIST train/test loss and accuracy for 5 layer fully connected network (RELU, Adam optimizer)](https://plon.io/files/58e40dba1b12ce00012bd735)

![Tensorflow MNIST train/test loss and accuracy for 5 layer fully connected network (RELU, Adam optimizer, dropout)](https://plon.io/files/58e40ec91b12ce00012bd737)

As we can see changing from sigmoid to RELU activation and use Adam optimizer increse accuracy over 2.5%, wich is 
significant for such small change. Howerver, adding dropout decrease , but if we compare test loss graphs
we can notice that dropout decrease the final test accuracy, but the test accuracy graph is much smoother.



## Convolutional neural network

### Network architecture

The network layout is as follows:
```
5 layer neural network with 3 convolution layers, input layer 28*28= 784, output 10 (10 digits)
Output labels uses one-hot encoding
input layer               - X[batch, 784]
1 conv. layer             - W1[5,5,,1,C1] + b1[C1]
                            Y1[batch, 28, 28, C1]
 
2 conv. layer             - W2[3, 3, C1, C2] + b2[C2]
2.1 max pooling filter 2x2, stride 2 - down sample the input (rescale input by 2) 28x28-> 14x14
                            Y2[batch, 14,14,C2] 
3 conv. layer             - W3[3, 3, C2, C3]  + b3[C3]
3.1 max pooling filter 2x2, stride 2 - down sample the input (rescale input by 2) 14x14-> 7x7
                            Y3[batch, 7, 7, C3] 
4 fully connecteed layer  - W4[7*7*C3, FC4]   + b4[FC4]
                            Y4[batch, FC4] 
5 output layer            - W5[FC4, 10]   + b5[10]
One-hot encoded labels      Y5[batch, 10]
```

As a optimizer I choose AdamOptimizer and all weights was randomly intialized from gausian distribution
with std=0.1. Activation function is relu, without dropout.


### Results

All results are for 5k iteration.

* five layer convolutional neural network with max pooling : **accuracy=0.9890**


![Tensorflow MNIST train/test loss and accuracy for convolutional 5 layer network](https://plon.io/files/595b0753c0265100013c2c07)



## Summarry

* Single layer neural network **accuracy=0.9237** 
* five layer fully-connected : **accuracy=0.9541**
* five layer fully-connected with relu activation function and Adam optmizer: **accuracy=0.9817**
* five layer fully-connected with relu activation, Adam optmizer and dropout: **accuracy=0.9761**
* five layer convolutional neural network with max pooling : **accuracy=0.9890**

## Download or run the project


* **[Clone project from github](https://github.com/ksopyla/tensorflow-mnist-convnets)**
* **[Run project online in Plon](https://plon.io/explore/tensorflow-mnist-convolutio/dNiShdMM47C6oe1D2)**




## Refernces and furhter reading

* [CS231n Convolutional Neural Networks for Visual Recognition](http://cs231n.github.io/convolutional-networks/)
* [Tensorflow and deep learning without a PHD](https://codelabs.developers.google.com/codelabs/cloud-tensorflow-mnist/) - very good tutorial showing how to build modern MNIST conv net. It was my inspiration for this tutorial :)
* [What is the difference between a Fully-Connected and Convolutional Neural Network?](https://www.reddit.com/r/MachineLearning/comments/3yy7ko/what_is_the_difference_between_a_fullyconnected/)
* [Tensorflow Examples by aymericdamien](aymericdamien/TensorFlow-Examples) - github repository with very useful and not so obious Tensorflow examples
* [Awesome tensorflow](https://github.com/jtoy/awesome-tensorflow) - A curated list of dedicated resources
* [Projects with #Tensorflow tag in plon.io](https://plon.io/explore/tag/tensoflow)


### Textminingonline blog
* [Dive Into TensorFlow, Part I: Getting Started with TensorFlow ](http://textminingonline.com/dive-into-tensorflow-part-i-getting-started-with-tensorflow)
* [Dive Into TensorFlow, Part II: Basic Concepts](http://textminingonline.com/dive-into-tensorflow-part-ii-basic-concepts)
* [Dive Into TensorFlow, Part III: GTX 1080+Ubuntu16.04+CUDA8.0+cuDNN5.0+TensorFlow](http://textminingonline.com/dive-into-tensorflow-part-iii-gtx-1080-ubuntu16-04-cuda8-0-cudnn5-0-tensorflow)
* [Dive Into TensorFlow, Part IV: Hello MNIST](http://textminingonline.com/dive-into-tensorflow-part-iv-hello-mnist)



### MetaFlow blog tutorial
* [TensorFlow: A primer](https://blog.metaflow.fr/tensorflow-a-primer-4b3fa0978be3)
* [TensorFlow: Shapes and dynamic dimensions](https://blog.metaflow.fr/shapes-and-dynamic-dimensions-in-tensorflow-7b1fe79be363)
* [TensorFlow: saving/restoring and mixing multiple models](https://blog.metaflow.fr/tensorflow-saving-restoring-and-mixing-multiple-models-c4c94d5d7125)
* [TensorFlow: How to freeze a model and serve it with a python API](https://blog.metaflow.fr/tensorflow-how-to-freeze-a-model-and-serve-it-with-a-python-api-d4f3596b3adc)