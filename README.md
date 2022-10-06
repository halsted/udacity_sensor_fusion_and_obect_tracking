# udacity_sensor_fusion_and_object_tracking

Note: I am using the Udacity workspace to develop and test my code.

## Step 1: Implement an EKF

![image](https://user-images.githubusercontent.com/7365421/193934012-585939ec-7154-486f-90a3-5cd63cceb080.png)
![image](https://user-images.githubusercontent.com/7365421/193934116-06c45298-3ff9-442b-9898-8a8daf9c84f6.png)

## Step 2: Track Management

![image](https://user-images.githubusercontent.com/7365421/193951569-93b230c8-8ed1-4a6b-82dd-a26df35e2fca.png)

## Step 3: Simple Nearest Neighbor (SNN) Data Association

![image](https://user-images.githubusercontent.com/7365421/193958489-296f63c9-c678-4ac1-a184-ef5543abbcc7.png)
![image](https://user-images.githubusercontent.com/7365421/193958553-d7bae22c-14f3-4c77-b2db-b23a00929546.png)

## Step 4:  Nonlinear Camera Measurement Model and Camera-Lidar Fusion Module

![image](https://user-images.githubusercontent.com/7365421/194413664-9a56d6e6-a193-4f7b-b206-df5615b976f8.png)
![image](https://user-images.githubusercontent.com/7365421/194413786-f2c53e45-e8c0-4364-8af5-7ce0a66e3c9d.png)

## Write-up Questions

### Write a short recap of the four tracking steps and what you implemented there (EKF, track management, data association, camera-lidar sensor fusion). Which results did you achieve? Which part of the project was most difficult for you to complete, and why?

Step 1: EKF - This seemed to be a straight-forward implementation of the Extended Kalman Filter equations in python for 3D position and velocity. In practice, I think the hard thing about implementing an EKF algorithm would be deciding how to model the process noise (covariance matrix Q), the measurement noise (covariance matrix R) and the estimation error (covariance matrix P).

Step 2: Track Management - This also seemed very straight-forward programming task, especially given the exercises and lecture videos on track management.

Step 3: Multi-target Tracking - This worked quite well. In this step, only the lidar measurements were used, but vehicles could be tracked accurately.

Step 4: Sensor Fusion - In this part, I added code so that the camera is used along with the lidar using sensor fusion. The coding exercises in this part were not too difficult. However, I initially had a bug in my code so that confirmed Track #1 was deleted in Frame #198 and a tentative track took its place. The tracking worked well right up until the last 2 frames, when there was no lidar measurement associated with Track #1. For some reason, this didn't happen in Step 3 when using lidar alone, which was very strange. It turned out that the problem was in my gating function. I had been setting df as follows:  

if sensor.name == "lidar":  
&emsp;dof = 2  
else:  
&emsp;dof = 1  
limit = chi2.ppf(params.gating_threshold, df=dof)  

However, on Lesson 19 on gating, I read that the dof should equal the dimension of the measurement space so I changed the code to:  

limit = chi2.ppf(params.gating_threshold, df=sensor.dim_meas)  

which increased the dof by 1 for each sensor. This solved my problem!!! I had tried changing other parameters without success. Once the bug was fixed, I got good results for Step 4, as shown above.

### Do you see any benefits in camera-lidar fusion compared to lidar-only tracking (in theory and in your concrete results)?

In theory, I see great benefit in camera-lidar fusion because each sensor has different strenths and weaknesses. Having multiple sensor types and implementing sensor fusion should decrease uncertainty and make the system more reliable. In the very limited testing that I did (i.e. a single sequence), I didn't see much, if any, improvement using camera-lidar fusion compared to lidar-only. They both worked well so in this case lidar alone was sufficient. I believe that there could be many situations where a camera could detect a vehicle or pedestrian that the lidar might miss.

### Which challenges will a sensor fusion system face in real-life scenarios? Did you see any of these challenges in the project?

Sensor fusion is a good idea because it adds redundancy and reduces uncertainty. Since safety is paramount in self-driving cars, having multiple sensor types is extremely important. One of the main challenges in real-life is the cost associated with lidar, since it is still very expensive. Since we used the Waymo dataset, we didn't have to worry about the lidar cost.

### Can you think of ways to improve your tracking results in the future?

Adding multiple cameras with different viewing angles would be helpful. Of course, this increases processing complexity but as long as our hardware has sufficient computational performance, it should help improve tracking results.

