## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Advanced Lane Finding Project**

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

[image1]: ./output_images/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./output_images/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./examples/example.ipynb" (or in lines # through # of the file called `some_file.py`).  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function.

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:


#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

The code was below the title "Find a good threshold for images".
I used a combination of color and gradient thresholds to generate a binary image.

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

My code for perspective transform was below the title "Find perspective transform matrix".
First,I undistorted the image, then I found four points to circle the area within the lane.Next,I found the best four destination points by trying different value and seeing the result,then used the `cv2.getPerspectiveTransform()` function to get the transform matrix.After that,I used `cv2.warpPerspective` to warp the image "test_images/straight_lines1.jpg".

I verified that my perspective transform was working as expected by drawing two parallel line on the warped image.Here is the result:

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

The code was under the title "Detect lane pixels and fit to find the lane boundary".

I identified lane-line pixels with 5 steps.First,I used 390(pixel) and 920(pixel) as the base of the lane lines and used that as starting point for where to search for the lines,then I used a sliding window,to found and followed the lines up to the top of the image.Next,I used the points I found to fit a second order polynomial.After that, I used the line I found from the first image as a base to found the lines for the following pictures,then fitted to a new second order polynomial.

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The code for calculated the radius of curvature of the lane and the position was in function `fit()` (the second half) and `process_image()` (under the comment # vehicle position)respectively.

I calculated the radius with 3 step.First turn the pixels I found from pixels space to meters,then fit to another second order polynomial,then I used the formula from the lecture to calculate the radius.

I calculated the position by finding the midpoint of the left and right lane,then subtracted 640(the midpoint of the image).After that,converted the value from pixels space to meters.


#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The code for this part was below the title 
"Try on one image".  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

It was not robust enough when there was shadow when I first tried with only S channel and gradient thresholds (the video was output.mp4).It was much more robust after I tried L channel thresholds.
 
It will fail when it is very dark,like the image from the challenge_video.mp4,I think maybe I will just use the polynomial I found from previous image and fit it to the new image since the lane doesn't change much.
 
