# Behaviorial Cloning Project

## Problem approach

### model

I used VGG net 11 layers, but it too large for this problem, so I reduced filter size. for detail of model architecture please see in the next section. I trained model from data of center lane and recovering data but I got problem when the car hit on off road trail, so I added more data of preventing off road trail.
![prevent off road trail image](/examples/off_road.jpg?raw=true "prevent off road trail image")
![recovering image](/examples/recovering.jpg?raw=true "recovering image")

### data generation

I collected data from center lane, recovering from side road and prevent goning on off road.

### data processing

For data processing, I removed 90% of 0 angle so model will not overtrain on 0 angle. And for recovering data, I removed angle between 1 to -1 because this range of angle is going straight angle and it will influence other data
![data before process](/examples/1.jpg?raw=true "data before process")
![data after process](/examples/2.jpg?raw=true "data after process")

## Details About Files In This Directory

### `model.py`

This file has 2 part.

1. preprocess data, line 9-103
* data of center lane, prevent off road : remove 90% of 0 angle, so it take short time to train and model will not be train on a lot of 0 angle.
* recovering data : remove angle between 1 to -1, so this data will not influence other data (use only turning data).

2. model line 114-182 : This model initialize by VGG net 11, but I decrease filter size down to 8-64. All activation function is relu.

Here is architecture of the model:

* Normalize (divide 255 and minus 5)
* Crop, input shape (160, 320, 3), output shape (60, 320, 3)
* Convolution layer 1, input shape (64, 320, 3), filtter shape (3, 3, 8), output shape (64, 320, 8)
* MaxPooling, input shape (64, 320, 8), output shape (32, 160, 8)
* Convolution layer 2, input shape (32, 160, 8), filtter shape (3, 3, 16), output shape (32, 160, 16)
* MaxPooling, input shape (32, 160, 16), output shape (16, 80, 16)
* Convolution layer 3, input shape (16, 80, 16), filtter shape (3, 3, 32), output shape (16, 80, 32)
* Convolution layer 4, input shape (16, 80, 32), filtter shape (3, 3, 32), output shape (16, 80, 32)
* MaxPooling, input shape (16, 80, 32), output shape (8, 40, 32)
* Convolution layer 5, input shape (8, 40, 32), filtter shape (3, 3, 64), output shape (8, 40, 64)
* Convolution layer 6, input shape (8, 40, 64), filtter shape (3, 3, 64), output shape (8, 40, 64)
* MaxPooling, input shape (8, 40, 64), output shape (4, 20, 64)
* Convolution layer 7, input shape (4, 20, 64), filtter shape (3, 3, 64), output shape (4, 20, 64)
* Convolution layer 8, input shape (4, 20, 64), filtter shape (3, 3, 64), output shape (4, 20, 64)
* MaxPooling, input shape (4, 20, 64), output shape (2, 10, 64)
* Flatten, input shape (2, 10, 64), output shape (1280)
* Fully connected layer 9, input shape (1280), output shape (1280)
* Dropout rate 50%
* Fully connected layer 10, input shape (1280), output shape (640)
* Dropout rate 50%
* Fully connected layer 11, input shape (640), output shape (1)

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
