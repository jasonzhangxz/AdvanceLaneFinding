# **Advanced Lane Finding Project**

## Introduction

### This project is to write a software pipeline to to first detect lanes on a static image, then take a short video as input and process the video (as continuous image frames)to detect the lanes in the recorded video. In the meantime calculate the road curvature and the car's offset from the road center.

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./writeup_images/chessboardUndistored.png "Undistorted"
[image2]: ./writeup_images/straightlines2.jpg "original image"
[image3]: ./writeup_images/binary.png "Binary Example"
[image4]: ./writeup_images/birdView.png "Warp Example"
[image5]: ./writeup_images/slidingWindow.png "Fit Visual"
[image6]: ./writeup_images/output.png "Output"
[image7]: ./writeup_images/finalImage.png "finalImage"
[video1]: ./project_video_output.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Project Writeup

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second code cell (Under Calibrate the Camera) of the IPython notebook named as "AdvancedLaneLine.ipynb" .  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result:

![chessboardUndistored][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![straightlines2][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color RGB-R and HLS-L thresholds to generate a binary image (code cell under 'Threshold Functions').  Here's an example of my output for this step.  Take the above road image as an example.

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform is under 'Create the Perspective view - BirdEye view', the code cell of the IPython notebook.  This function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

It is defined as following source and destination points:

| Source        | Destination   |
|:-------------:|:-------------:|
| 258, 675      | 400, 720        |
| 1053,675      | 900, 720      |
| 578, 458     | 400, 0     |
| 707, 458      | 900, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![warp example][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

First take a histogram of the warped image (only the ROI road info), by finding the peak of the left and right halves of the histogram, it will find the starting point for the left and right lines. Then create a sliding window to find all pixels belongs to left and right lanes. Then fit a 2nd order polynomial to each lane using `np.polyfit`. The visualization of the sliding window fit polynomial looks like below:

![slidingWindow][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

These can be found in code cells under ' Measure the curvature of the road ' and ' Measure the vehicle's offset from the center of the road '.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented a function `drawDriveArea()` which can be found under ' Draw drivable area '.  Here is an example of my result on a test image:

![drivableArea][image6]

Finally, write the text of the curvature and center offset data onto the image.
![finalImage][image7]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

For this project, once you build up the pipeline to process the image/video, I think the tricky part is to create a good threshold function to generate the binary image with less noise but still keeping the lane features. I found my solution of using the combination of gradx and grad_dir may have problems when the lighting conditions change or shadows on the road/shoulder. In the future, probably try different colorspace and different combination of different thresholds to try on the challenge videos to see how it goes.
