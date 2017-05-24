# Advanced Lane Finding Project

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

[image1]: ./output_images/chessboard_output.png "Chess Corners"
[image2]: ./output_images/undistort_output.png "Undistorted"
[image3a]: ./test_images/test1.jpg "Test Image"
[image3b]: ./output_images/undistorted/test1.jpg "Undistorted Test Image"
[image4]: ./output_images/binary_output.png "Binary Output"
[image5]: ./output_images/warped_output.png "Road Transformed"
[image6]: ./output_images/fit_lines_output.png "Fit Visual"
[image7]: ./output_images/radius_of_curvature.png "Radius of Curvature"
[image8]: ./output_images/final_output.png "Output"
[video1]: ./output_videos/project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it! 
### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the code cell #9 of the IPython notebook.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

Here are chessboard images with corners indentified and drawn.

![alt text][image1]

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function. The code for this step is in the code cell #11 of the IPython notebook in function `undistort_image`. I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image2]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.
To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this:
![alt text][image3a]

Here's the image after distortion correction.

![alt text][image3b]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
I used a combination of color and gradient thresholds to generate a binary image (function `threshold_image` at the code cell #13 of the IPython notebook).  Here's an example of my output for this step.

![alt text][image4]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

I chose to hardcode the source and destination points in the following manner:


| Source        | Destination   | 
|:-------------:|:-------------:| 
| 253, 697      | 303, 697      | 
| 585, 456      | 303, 0        |
| 700, 456      | 1011, 0       |
| 1061, 690     | 1011, 690     |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image. The code for this step is in the code cell #15 of the IPython notebook.

![alt text][image5]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

After doing undistortion, color and gradient thresholding and perspecive trannsform, I did a sliding window search for detecting lane lines using the code provided in the lessons. Then I fitted a second order polynomial to it and drew the resulting lines in yellow. The code for this `detect_lanes` function is in the code cell #17 of the IPython notebook.
Here is an example output.

![alt text][image6]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in the code cell #19 of the IPython notebook in the function `radius_of_curvature`. I used the same code provided in the lessons. However to calculate the position of vehicle from the center of the road, I took some help from another student: https://github.com/upul/CarND-Advanced-Lane-Lines

![alt text][image7]

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I did this in the code cell #21 of the IPython notebook in the function `process_image`.  Here is an example of my result on a test image:

![alt text][image8]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./output_videos/project_video.mp4)
The code for my video processing pipeline is in code cell #23-25 of the IPython Notebook. It uses the Line class provided in the lessons with a buffer of 10 frames. I added the `process_image` function to this class to process video frames.

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The great thing about this project was that most of the code was already provided and explained in the lessons. Most of the code works as it is and the only challenging part is tuning the various parameters.
The only problem I faced was detecting vehicle position from the center of the road. For this I took help from another student: https://github.com/upul/CarND-Advanced-Lane-Lines.
My pipeline is a little wobbly at about 20 seconds in the project video and performs very poorly on challenge videos. I would like to make it more robust using some of the techniques mentioned in the section 'Tips and Tricks for the Project'. Also I would like to try the sliding window search approach mentioned in the lessons which is to apply a convolution, which will maximize the number of "hot" pixels in each window.
