# Behaviorial Cloning Project

## Problem approach

### model

I used VGG net 11 layers, but it too large for this problem, so I reduced filter size. for detail of model architecture please see in the next section.

### data generation

I collected data from center lane and recovering from side road. I trained model from data of center lane and recovering data but I got problem when the car hit on off road trail, so I added more data of preventing off road trail.<br/>
"center lane image"<br/>
![center lane image](/examples/center.jpg?raw=true "center lane image")<br/>
"prevent off road trail image"<br/>
![prevent off road trail image](/examples/off_road.jpg?raw=true "prevent off road trail image")<br/>
"recovering image"<br/>
![recovering image](/examples/recovering.jpg?raw=true "recovering image")<br/>

Moreover, I used all 3 cameras in car.<br/>
"left camera image"<br/>
![left camera image](/examples/left.jpg?raw=true "left camera image")<br/>
"center camera image"<br/>
![center camera image](/examples/center.jpg?raw=true "center camera image")<br/>
"right camera image"<br/>
![right camera image](/examples/right.jpg?raw=true "right camera image")<br/>

### data processing

For data processing, I flipped and cropped images, and this made huge amount of data and prevent overfitting in model.<br/>
"flipped image, left to right"<br/>
![flipped image](/examples/off_road_flipped.jpg?raw=true "flipped image")<br/>
"cropped image, from 160x320 to 64x320"<br/>
![cropped image](/examples/recovering_cropped.jpg?raw=true "cropped image")<br/>

So I removed 90% of 0 angle so model will not overtrain on 0 angle. And for recovering data, I removed angle between 1 to -1 because this range of angle is going straight angle and it will influence other data. This reduced amount of data.<br/>
"data before process"<br/>
![data before process](/examples/1.jpg?raw=true "data before process")<br/>
"data after process"<br/>
![data after process](/examples/2.jpg?raw=true "data after process")<br/>

## Details About Files In This Directory

### `model.py`

This file has 2 part.

1. preprocess data, line 9-103
* data of center lane, prevent off road : remove 90% of 0 angle, so it takes short time to train and model will not be train on a lot of 0 angle.
* recovering data : remove angle between 1 to -1, so this data will not influence other data (use only turning data).

2. model line 114-182 : This model initialized by VGG net 11, but I decreased filters size down to 8-64. All activation function are relu, all conv2D use same padding and all max pooling use valid padding.

Here is architecture of the model:

Layer (type)                | Output Shape            | Param #   
----------------------------|-------------------------|------------
lambda_1 (Lambda)           | (None, 160, 320, 3)     |  0         
cropping2d_1 (Cropping2D)   | (None, 64, 320, 3)      |  0         
conv2d_1 (Conv2D)           | (None, 64, 320, 8)      |  224       
max_pooling2d_1 (MaxPooling2| (None, 32, 160, 8)      |  0         
conv2d_2 (Conv2D)           | (None, 32, 160, 16)     |  1168      
max_pooling2d_2 (MaxPooling2| (None, 16, 80, 16)      |  0         
conv2d_3 (Conv2D)           | (None, 16, 80, 32)      |  4640      
conv2d_4 (Conv2D)           | (None, 16, 80, 32)      |  9248      
max_pooling2d_3 (MaxPooling2| (None, 8, 40, 32)       |  0         
conv2d_5 (Conv2D)           | (None, 8, 40, 64)       |  18496     
conv2d_6 (Conv2D)           | (None, 8, 40, 64)       |  36928     
max_pooling2d_4 (MaxPooling2| (None, 4, 20, 64)       |  0         
conv2d_7 (Conv2D)           | (None, 4, 20, 64)       |  36928     
conv2d_8 (Conv2D)           | (None, 4, 20, 64)       |  36928     
max_pooling2d_5 (MaxPooling2| (None, 2, 10, 64)       |  0         
flatten_1 (Flatten)         | (None, 1280)            |  0         
dense_1 (Dense)             | (None, 1280)            |  1639680   
dropout_1 (Dropout)         | (None, 1280)            |  0         
dense_2 (Dense)             | (None, 640)             |  819840    
dropout_2 (Dropout)         | (None, 640)             |  0         
dense_3 (Dense)             | (None, 1)               |  641       

Total params: 2,604,721<br/>
Trainable params: 2,604,721<br/>
Non-trainable params: 0<br/>


### `drive.py`

Usage of `drive.py` requires you have saved the trained model as an h5 file, i.e. `model.h5`. See the [Keras documentation](https://keras.io/getting-started/faq/#how-can-i-save-a-keras-model) for how to create this file using the following command:
```sh
model.save(filepath)
```

Once the model has been saved, it can be used with drive.py using this command:

```sh
python drive.py model.h5
```

The above command will load the trained model and use the model to make predictions on individual images in real-time and send the predicted angle back to the server via a websocket connection.

Note: There is known local system's setting issue with replacing "," with "." when using drive.py. When this happens it can make predicted steering values clipped to max/min values. If this occurs, a known fix for this is to add "export LANG=en_US.utf8" to the bashrc file.

#### Saving a video of the autonomous agent

```sh
python drive.py model.h5 run1
```

The fourth argument, `run1`, is the directory in which to save the images seen by the agent. If the directory already exists, it'll be overwritten.

```sh
ls run1

[2017-01-09 16:10:23 EST]  12KiB 2017_01_09_21_10_23_424.jpg
[2017-01-09 16:10:23 EST]  12KiB 2017_01_09_21_10_23_451.jpg
[2017-01-09 16:10:23 EST]  12KiB 2017_01_09_21_10_23_477.jpg
[2017-01-09 16:10:23 EST]  12KiB 2017_01_09_21_10_23_528.jpg
[2017-01-09 16:10:23 EST]  12KiB 2017_01_09_21_10_23_573.jpg
[2017-01-09 16:10:23 EST]  12KiB 2017_01_09_21_10_23_618.jpg
[2017-01-09 16:10:23 EST]  12KiB 2017_01_09_21_10_23_697.jpg
[2017-01-09 16:10:23 EST]  12KiB 2017_01_09_21_10_23_723.jpg
[2017-01-09 16:10:23 EST]  12KiB 2017_01_09_21_10_23_749.jpg
[2017-01-09 16:10:23 EST]  12KiB 2017_01_09_21_10_23_817.jpg
...
```

The image file name is a timestamp of when the image was seen. This information is used by `video.py` to create a chronological video of the agent driving.

### `video.py`

```sh
python video.py run1
```

Creates a video based on images found in the `run1` directory. The name of the video will be the name of the directory followed by `'.mp4'`, so, in this case the video will be `run1.mp4`.

Optionally, one can specify the FPS (frames per second) of the video:

```sh
python video.py run1 --fps 48
```

Will run the video at 48 FPS. The default FPS is 60.
