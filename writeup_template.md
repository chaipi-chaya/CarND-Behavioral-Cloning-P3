# **Behavioral Cloning** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Behavioral Cloning Project**

The goals / steps of this project are the following:
* Use the simulator to collect data of good driving behavior
* Build, a convolution neural network in Keras that predicts steering angles from images
* Train and validate the model with a training and validation set
* Test that the model successfully drives around track one without leaving the road
* Summarize the results with a written report

## Rubric Points
### Here I will consider the [rubric points](https://review.udacity.com/#!/rubrics/432/view) individually and describe how I addressed each point in my implementation.  

---
### Files Submitted & Code Quality

#### 1. Submission includes all required files and can be used to run the simulator in autonomous mode

My project includes the following files:
* model.py containing the script to create and train the model
* drive.py for driving the car in autonomous mode
* model.h5 containing a trained convolution neural network 
* writeup_report.md or writeup_report.pdf summarizing the results

#### 2. Submission includes functional code
Using the Udacity provided simulator and my drive.py file, the car can be driven autonomously around the track by executing 
```sh
python drive.py model.h5
```

#### 3. Submission code is usable and readable

The model.py file contains the code for training and saving the convolution neural network. The file shows the pipeline I used for training and validating the model, and it contains comments to explain how the code works.

### Model Architecture and Training Strategy

#### 1. An appropriate model architecture has been employed

My model consists of a convolution neural network with 5x5 and 3x3 filter sizes RELU activations and depths between 24 and 64 (model.py lines 96-108) 

The data is normalized in the model using a Keras lambda layer (code line 90). 

#### 2. Attempts to reduce overfitting in the model

The model contains dropout layers in order to reduce overfitting (model.py lines 117 and 123). 

The model was trained and validated on different data sets to ensure that the model was not overfitting (code line 135). The model was tested by running it through the simulator and ensuring that the vehicle could stay on the track.

#### 3. Model parameter tuning

The model used an adam optimizer, so the learning rate was not tuned manually (model.py line 132).

#### 4. Appropriate training data

Training data was chosen to keep the vehicle driving on the road. I used a combination of center lane driving, two recovering from off road trail.

For details about how I created the training data, see the next section. 

### Model Architecture and Training Strategy

#### 1. Solution Design Approach

1). I made model from VGG net 11 layers but I decrease filter size down to 8-64. 

2). I splited data into training set and validation set. I found that my first model had a low mean squared error on the training set but a high mean squared error on the validation set. This implied that the model was overfitting. 

3). To prevent overfitting I added 2 layers of dropout with rate 0.5 which was working well 

4). At the end of the process, the vehicle is able to drive autonomously around the track without leaving the road.

#### 2. Final Model Architecture

The final model architecture (model.py lines 122-182) consisted of a convolution neural network, flatten, fully-connected, dropout layers

#### 3. Creation of the Training Set & Training Process+

1). I collected data of center lane driving only 1 lap and recovering from sides road. For data of center lane, I removed 90% of 0 angle because it got a lot of it, so model will not be train on a lot of 0 angle. For recovering data, I removed angle between 1 to -1, so I used only data when the car turn and it will not influence center data.

2). I randomly shuffled the data set and put 20% of the data into validation set.

3). I used this training data for training the model. The validation set helped determine if the model was over or under fitting. The ideal number of epochs was 10 as evidenced by loss of both training set and validation set I used an adam optimizer so that manually training the learning rate wasn't necessary.

4). I tested model with simulator and it worked fine until the car didn't turn and went off road trail, so I added more lap data of center lane and still didn't work.

5). I collected data only prevent off road trail part which had 2 of them, processed same way as data fo center lane, added to model and it worked very well, but only problem was it take a lot of time to train because it had huge data.

6). I removed second lap of center lane driving and retrained it. It worked fine and took little time to train.
