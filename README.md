# Terrain-Identification

## Dataset Description

Here is a brief description of the data:

  - "_x" files contain the xyz accelerometers and xyz gyroscope measurements from the lower limb.
  - "_x_time" files contain the time stamps for the accelerometer and gyroscope measurements. The units are in seconds and the sampling rate is 40 Hz.
  - "_y" files contain the labels. (0) indicates standing or walking in solid ground, (1) indicates going down the stairs, (2) indicates going up the stairs, and (3) indicates walking on grass.
  - "_y_time" files contain the time stampes for the labels. The units are in seconds and the sampling rates is 10 Hz.
  
  Download dataset from [here](https://drive.google.com/file/d/1sRx0JY8it0lZH_foOclPXJ04a2tI--oI/view?usp=sharing)
  
 ## Approach
 
Accelerometer and gyroscope are the two most common sensors that are used for terrain identification. Accelerometer and gyroscope data, for example, are represented by a set of three vectors acc[i] = (xi, yi, zi) and gyro[i] = (xi, yi, zi), where i = (1, 2, 3, . . . , n). We analyzed the data for different test subjects and found that the sampling rate for x is 40Hz and sampling rate for y is 10Hz. For every 0.1s of y there are 4 samples in the x. We extrapolated the label from y to correspond to 4 rows for sensor measurements. Therefore, each sensor measurement consisting of 6 data-points (x1, y1, z1, x2, y2, z2) along with its extrapolated label becomes our single data instance. We then form sequences of such data points as our training instances for the Bi-LSTM model. We experimented with several different lengths of sequences and configurations and chose the best one as our model to solve terrain identification problem. The advantage of using LSTM’s for sequence classification is that they can learn from the raw time series data directly and thus do not require domain expertise to manually engineer input features.

## Model Training

* We found that for every four instances of the accelerometer and gyroscope values, there is a corresponding label. We extrapolated the label from y to correspond to 4 rows for sensor measurements. Therefore, each sensor measurement consisting of 6 data-points (x1, y1, z1, x2, y2, z2) along with its extrapolated label becomes our single data instance.
* We know that the dataset is skewed heavily with 0 label being the most common. The distribution of overall dataset can be seen in table I. The corresponding importance
that should be given to each class while training is also presented which we calculated using the scikit-learn package.
* We used each file as input instance rathar than merging all files into one. We used overlapping windowing approach to capture the continuous representation of data. We later concatenated the windowed data from each file. The final shape of the dataset will be total-windows * window-size * 6 for a single input file.

<p align="center">
  <img src="https://github.com/kdave97/Terrain-Identification/blob/master/window-size.PNG">
</p>

## Hyperparamter Tuning
* Window Size - The size of the window determines the time taken by the robot to travel on a terrain. We decided to use window size of 30 on data from x which is equivalent to 0.75s and also a window size of 60 which represents 1.5s. 

| Window Size | Accuracy | F1 Score |
| ----------- | -------- | -------- |
|     60      |   0.95   |   0.93   |
|     30      |   0.93   |   0.90   |

* Hidden Units - The number of hidden units determine the capability of the model to generate various features based on the data.

| Hidden Units | Accuracy | F1 Score |
| ------------ | -------- | -------- |
|     128      |   0.95   |   0.93   |
|     256      |   0.91   |   0.88   |

* Step Size - Step size is an important parameter in determining how much the model should skip in batch while training. This will help in eliminating some of the data which can create anomaly in the dataset.

|  Step Size   | Accuracy | F1 Score |
| ------------ | -------- | -------- |
|     1      |   0.95   |   0.93   |
|     4      |   0.92   |   0.90   |
|     30      |   0.86   |   0.82   |

