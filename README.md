# PicoBoo - Security System with Motion Tracking using Thermal Imaging and Deep Learning

April 2022 | Mini Project in TY BTech | Read: [Project Report](https://github.com/anwaypimpalkar/picoboo-thermal-imaging-surveillance-system/raw/main/docs/Report%20-%20PicoBoo%20-%20Security%20System%20with%20Motion%20Tracking%20using%20Thermal%20Imaging%20and%20Deep%20Learning.pdf)

## Introduction

The *PicoBoo* system we developed is built on the foundations of thermal imaging and deep neural networks. The application is built and then deployed on a Raspberry Pi 4 Model B for testing and prototyping. We first collected data from a FLIR T420bx Thermal Imaging Camera, cleaned the data and made a usable training dataset of human images from it. To implement a model which enables tracking of humans in a thermal camera video stream, we used the EfficientNet-Lite neural network architecture family and gained a high accuracy coupled with a low latency on it. These detections were sent to Firebase, which was accessed through a vanilla HTML/CSS/JS app showing whether a human was detected in the frame or not. This web application was deployed to Heroku, which can be easily accessed over web browser. The focus in this repository is more inclined towards the Deep Learning aspect in this project.

## High Level Overview

The project is a two-part system encompassing the following subsystems:

1. Human detection and tracking using a thermal imaging camera using TensorFlow Lite.
2. Web interface to show the status of the security system and stream the thermal camera stream to the application/interface.

A generic block diagram of the entire system is shown below.

![](https://github.com/anwaypimpalkar/picoboo-thermal-imaging-surveillance-system/raw/main/docs/blockdiagram.png)

## Human Detection and Tracking

The human detection and tracking algorithm can be simplified into three steps:

1. Collecting data using the FLIR T420bx Thermal Imaging Camera.
2. Cleaning the data, labeling it, and building a dataset suitable to train a detection model.
3. Building a machine learning model capable of detecting humans in the frames.

#### Collecting Data and Building a Dataset

The data was collected using the FLIR T420bx Thermal Imaging Camera using two methods, either by taking pictures and videos on the camera itself and extracting it from the SD card, or by connecting the camera to a computer and accessing the stream via the FLIR Thermal Studio. Since the system we developed was for human intruder detection, we took images and videos focusing on two main labels - human subjects and backgrounds. We collected a total of 6,067 images with human subjects and 4,937 instances of backgrounds. Of this, we worked with a small portion of this data to build the model, of 184 instances of humans, and 222 instances of backgrounds.To label this data, we used a free open-source data labeler, [LabelImg](https://github.com/tzutalin/labelImg).

![](https://github.com/anwaypimpalkar/picoboo-thermal-imaging-surveillance-system/raw/main/docs/rawdata.png)

#### Object Detection Model using EfficientNet-Lite

Google released a family of image classification models called EfficientNet, which achieved state-of-the-art accuracy with an order of magnitude of fewer computations and parameters. EfficientNet has a reputation for achieving high accuracy with minimal parameters and FLOPS (Floating Point Operations Per Second). They looked to optimize these models for deployment at the edge, and in 2020 they launched EfficientNet-Lite which runs on TensorFlow Lite and is designed for performance on mobile CPU, GPU, and EdgeTPU. EfficientNet-Lite brings the power of EfficientNet to edge devices and comes in five variants, allowing users to choose from the low latency/model size option (EfficientNet-Lite0) to the high accuracy option (EfficientNet-Lite4).

We trained on the EfficientNet-Lite0 and EfficientNet-Lite2 model. The EfficientNet-Lite0 model gave a mean Average Precision (mAP) of 67.7% on the validation dataset. When the model ran on a given video stream, it ran at approximately 6 FPS. In an attempt to increase the accuracy, we also trained the model using EfficientNet-Lite2 which gave a higher mAP of 75.3%, which reduced to 72.6% post-quantization. Upon running inferences on our video stream, the frame rate dropped to approximately 2 FPS, which was impractical for our application.

![](https://github.com/anwaypimpalkar/picoboo-thermal-imaging-surveillance-system/raw/main/docs/outputs.png)

Using the Firebase Realtime Database API provided for Python, we sent the number of humans detected in the frame to the NoSQL cloud database , which was immediately reflected in it. If the number of detections on the Firebase Realtime Database is more than zero, then the page indicates that the security system is not safe and sends an alert of the intruder. This is triggered immediately due to the event handler which is set off as soon as there is a change in the database reference point.