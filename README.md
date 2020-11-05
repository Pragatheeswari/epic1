# Deep Convolutional Generative Adversial Network(DCGAN) for OASIS Brain data-set

## Description

The main idea of GAN is to generate the new random images(fake images), which looks more realsitic to the input images. It consists of Generator - to generate fake images, Discriminator - to classify the image as fake or real, and adversarial network that pits them against each other. 

DCGAN is an extension to the GAN architecture with the use of convolutional neural networks in both generator and discriminator part. Implenentation of the DCGAN model here, follows the the paper [Unsupervised Representation Learning With Deep Convolutional Generative Adversarial Networks](https://arxiv.org/pdf/1511.06434.pdf) by Radford et. al. It suggests the constraints on the model that required to effectively develop the high-quality generator model.

## Problem

Generating new brain images can be seen as a random variable generation problem, a probabilistic experiment. The sample image from the input OASIS brain dataset is shown below. Its 256 X 256 size with 1 grey-scale channel. Each image is a vector of 65,536-dimensions. We build a space with 65,536 axes, where each image will be a point in this space. Having a probability distribution function, that maps the each input brain images to the non-negative real number and sums to 1. GAN generates the new brain image by generating the new vector following this probability distribution over the 65,536-dimensional vector space, which is very complex one and we dont know how to generate this complex random variables. The idea here is an transform method, generating 65,536 standard random normal variables(as a noise vector) and apply the complex function to this variable, where this complex functions are approximated by the CNN in the generator part and produce the 65,536-dimensional random variable that follow the input brain images probability distribution.

![](https://github.com/Pragatheeswari/epic1/blob/master/input_samples.png)

## Modeling

GanModel.py module has both the generator and discrimininator network. Generator Networks takes Input as a noise vector of shape (100,), generated from the standard normal distribution and in the early layer of network its reshaped to the 65536. Remaining 5 layers consists of convolutional-2DTranspose layer for upsampling the image to size (256,256) with stridded convolutions, Batch normalisation and Relu activation functions. Output Layer consists of elu activation and produce the fake image of size (256,256,1).

Discriminator takes input image( either real or fake) of size (256,256,1). It inculdes 6 convolution-2D layer with convolution strides for downsampling with LeakyRelu activation and used droplayer with 0.3 for regularisation. Output layer uses sigmoid activation for classifying real(1) or fake(0) images.

Task-6 is the main file, it consists of function to load the images(data_gen), function to display the images produced by the generator network( display_images), other model parameters, building the models(generator, discriminator and combined networks), and finally training process for the nubmer of epoches.

## Training

Training the GAN involves training the generator part to maximize the final binary classification error ( between real or fake image), while discriminator is trained to minimize it. Reaches equilibrium when discriminator classify with equal probability and generator produces images that follows the input brain images probability distribution.

### Training procedure
Combined model is built by adding discriminator on top of generator. Compile the discriminator and set the training to false before compiling the combined model, so that only generator's model parameter are only updated. And not nessecary on compiling the generator model as its not ran alone. 
1. Reading the real-images of Batch-size(16), using data-gen() from the OASIS Dataset.
1. Using generator to generate fake images by giving input as a random noise vectors for a Batch-size(16).
1. Train the discriminator with both real(1) and fake(0) images to classify. This will allow the discriminator's weights to be updated.
1. Train the combined model only using the fake images.Note when combined model is trained only generator's model parameter is updated.
