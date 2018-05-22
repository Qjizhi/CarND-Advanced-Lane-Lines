## Writeup

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

[image1]: ./output_images/undistorted_images/calibration11.jpg "Undistorted"
[image2]: ./camera_cal/calibration2.jpg "Original Image"
[image3]: ./test_images/test5.jpg "Test Image"
[image4]: ./output_images/undistorted_images/test6.jpg "Undistorted"
[image5]: ./output_images/threshold_images/test6.jpg "Thresholded Image"
[image6]: ./output_images/perspective_images/test6.jpg "Perspective Transform"
[image7]: ./output_images/output/test6.jpg "Final Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the 2nd and 3rd code cell of the IPython notebook located in "./P4_Advanced_Lane_Lines.ipynb"

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

Original | UnDistorted
:--------:|:------------:
![alt text][image2] | ![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:

Original | UnDistorted
:--------:|:------------:
![alt text][image3] | ![alt text][image4]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image , code for thresholding is implemented under function 'threshold' defined in cell 4 of "./P4_Advanced_Lane_Lines.ipynb". To generate binary file i have ored Hue , Saturation Binaries anded them with X Gradient Binary. My focus was on achieving maximum lane detection even under tree shadow. For generating Hue Binary i have used X Gradient threshold. Here's an example of my output for this step.  

Original | Thresholded
:--------:|:------------:
![alt text][image3] | ![alt text][image5]


#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `perspective_transform()`, which appears in the 6th code cell of "./P4_Advanced_Lane_Lines.ipynb".  The `perspective_transform()` function takes as inputs an image (`img`), as well as source (`src`).  I chose the hardcode the source and destination points in the following manner:

```python
dst = np.float32([[(350, 720), (350, 0), (980, 0), (980, 720)]])
points = np.float32([[(200, 720), (570, 470), (720, 470), (1130, 720)]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 200, 720      | 350, 720        | 
| 570, 470      | 350, 0      |
| 720, 470     | 980, 0      |
| 1130, 720      | 980, 720        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

Original | Perspective Transformed
:--------:|:------------:
![alt text][image3] | ![alt text][image6]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

The code for lane-line pixling is done under the function `find_lanes()`, which appears in the 7th code cell of "./P4_Advanced_Lane_Lines.ipynb".  The `find_lanes()` function takes as inputs a warped image (`binary_warped`). This function shall search for the lane lines and then fit a 2nd order polynomial. Histogram method shared in UDACITY course was used. Complete image search is done as each test image is different. 

To draw the lane lines i have implemented the code in another function called 'draw_lines' which is in 8th code of "./P4_Advanced_Lane_Lines.ipynb". This function shall highlight the left and right lanes in red , blue colors respectively and the area in between the lanes is filled with green. Most of the code for this function implementation was taken from UDACITY course. The lane lines are drawn on unwarpped image and the output is added to the original image. To unwarp Inverse Perspective Transform matrix from Perspecive Transform is used. 

Following image shows the behavior of this function. 

Original | Final Output
:--------:|:------------:
![alt text][image3] | ![alt text][image7]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

To calculate the radius of curvature the function "cal_radius_curvature()" was implemented. This function is in 10th cell of "./P4_Advanced_Lane_Lines.ipynb". This function accepts Left and Right X,Y pixel positions. These pixels are coverted into meters and a 2nd order polynomial is fitted. Using the formula definied in the course R​curve​​=((1+(2Ay+B)^2​​)^3/2)/​(2A)​​​​​​  Left and Right Lane curvature is measured and returned. 

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

To draw the lane lines i have implemented the code in another function called 'draw_lines' which is in 8th code of "./P4_Advanced_Lane_Lines.ipynb". This function shall highlight the left and right lanes in red , blue colors respectively and the area in between the lanes is filled with green. Most of the code for this function implementation was taken from UDACITY course. The lane lines are drawn on unwarpped image and the output is added to the original image. To unwarp Inverse Perspective Transform matrix from Perspecive Transform is used. Here is an example of my result on a test image:

Original | Final Output
:--------:|:------------:
![alt text][image3] | ![alt text][image7]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./output_videos/project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?
For processing the video file i have extracted each image and passed on the image to the function process_image implemented in 11the code cell of "./P4_Advanced_Lane_Lines.ipynb". This function shall perform the following steps
    - Undistort the image using the calibration matrix
    - generate binary threshold Image 
    - perform perspective transform
    - convert to gray scale
    - find lane lines and update the Right, Left lane object (this includes polynomial coeffiecients and lane line x,y points averaging)
    - draw the lane lines 
    - measure the current lane line curvature and average 
    - measure the offset.

Problems Faced:
I faced problem while performing perspective transform , even after several attempts my source and destination points were not accurate. I looked into UDACITY forums for the solution. 

Failure Scenarios:
My pipeline fails for challenge videos. Radius of curvature is not very accurate when the vehicle moves on a straight road radius of curvature is in the of 10s of thousand meters which is not very accurate. 
