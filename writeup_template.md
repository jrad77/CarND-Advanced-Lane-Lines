## Advanced Lane Finding Project

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

[image1]: ./output_images/undistort_calibration.png "Undistorted"
[image2]: ./output_images/undistort_output.png "Road Transformed"
[image3]: ./output_images/theshold_result.png "Thesholding Examples"
[image4]: ./output_images/undistort_and_warped.png "Warp Example"
[image5]: ./output_images/lane_fitting.png "Fit Visual"
[image6]: ./output_images/full_pipeline.png "Output"
[video1]: ./output.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second code cell of the IPython notebook located in "./lane_finding.ipynb".

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

Here is a demonstration of the distortion correction on a road image:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of thresholding on the S channel from the HLS space and a gradient thresholds in the x direction to generate a binary image in the fourth code cell of my notebook.  Here are some an examples of my output for this step.  

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `transform_persepctive()` and `reverse_transform_perspective()`, which appears in fifth cell of the notebook.  The `transform_persepctive()` function takes as inputs an image (`img`) and performs a perspective transform on it. The functor has the camera matrix M captured with it, so it does not need to be provided. I chose the hardcode the source and destination points for calculating the M matrix by externally viewing the image with edisplay and finding the coordinates in the image itself.

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 582, 460      | 313, 200      | 
| 702, 460      | 941, 200      |
| 1100, 719     | 941, 719      |
| 205, 719      | 313, 719      |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I fit my lane lines with a 2nd order polynomial in the `detect_lines()` function in the seventh cell of the notebook. The results look like this:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in the function `calc_curvature()` in the seventh code cell of the notebook.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in the `draw_overlay()` function in the eighth code cell.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a video showing the lane fitting. There are some small imperfections but nothing catastrophic. This could easily be smoothed out by using information from the
previous fittings and doing some outlier detection.

![Link to my video example][video1]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The current pipeline is a solid starting point but is not very robust. More complex examples with trickier lighting and worse road conditions could easily make it fail. Using the time of day or the amount of light present in the image could be used as a heuristic for using a different pipeline. Simple additions like keeping a running weighted average of the lane lines, and rejecting outliers would make this more robust.
