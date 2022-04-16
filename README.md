# PicoBoo - Security System with Motion Tracking using Thermal Imaging and Deep Learning

January - May 2022 | Mini Project in TY BTech | Read: [Project Report](https://github.com/anwaypimpalkar/picoboo-thermal-imaging-surveillance-system/raw/main/docs/Report%20-%20PicoBoo%20-%20Security%20System%20with%20Motion%20Tracking%20using%20Thermal%20Imaging%20and%20Deep%20Learning.pdf)

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

1. Collecting data using the *FLIR T420 bx Thermal Imaging Camera*.
2. Cleaning the data, labeling it, and building a dataset suitable to train a detection model.
3. Building a machine learning model capable of detecting humans in the frames.