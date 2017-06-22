# **Advanced Lane Finding Project**
---

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

---

In the following discussion I will consider the [rubric points](https://review.udacity.com/#!/rubrics/571/view) individually and describe how I addressed each point in my implementation. The link to my GitHub repository can be found [here](https://github.com/urs-waldmann/CarND-Advanced-Lane-Lines).

---

## Camera calibration using chessboard images

The code for this step is contained in the first code cell in section "Camera calibration using chessboard images" of the IPython notebook located in "./adv_lane_fin.ipynb". I used the code provided by Udacity in the IPython notebook located in "./examples/example.ipynb".  

The code starts by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here the code is assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time the code successfully detects all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.
I added some code to save all successful or unsuccessful chessboard detections (at the moment this code is commented out). The images are located in "./output_images" and named calibrationX, where X runs from 1 to 20.
Here is an example image of an unsuccessful and a successful chessboard detection.

Unsuccessful chessboard detection:
![Unsuccessful chessboard detection](./output_images/calibration1)

Successful chessboard detection:
![Successful chessboard detection](./output_images/calibration2)

## Build an advanced lane line finding pipeline

In order to write a working pipline I first defined some helper functions that you can find in the sectin "helper function". I will discuss them in the sections where I used them.
In general I first built my pipeline step by step applying each step to the test images located in "./test_images". When I was satisfied with the current step I saved the modified test images in the folder "./output_images". All the code for saving test images is commented out at the moment.
In addition I added a visualization at the end of this section displaying every step on an example road image.

### Pipeline (single images)

#### Distortion correction

For this purpose I defined the helper function called `cal_undistort()`. This helper function uses the `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function. Then it applies this distortion correction to the test images called `img` using the `cv2.undistort()` function. Applying this helper function to one of the test images leads to this result:

![Undistorted road image][./output_images/undistorted_test1]

In order to check wether the distortion correction is good enough I also performed an undistortion on a chessboard image for reference.

Distorted chessboard image:
![Distorted chessboard image](./camera_cal/calibration1)

Undistorted chessboard image:
![Undistorted chessboard image](./output_images/undistorted_chessboard)

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at lines # through # in `another_file.py`).  Here's an example of my output for this step.  (note: this is not actually from one of the test images)

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warper()`, which appears in lines 1 through 8 in the file `example.py` (output_images/examples/example.py) (or, for example, in the 3rd code cell of the IPython notebook).  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
    [[(img_size[0] / 2) - 55, img_size[1] / 2 + 100],
    [((img_size[0] / 6) - 10), img_size[1]],
    [(img_size[0] * 5 / 6) + 60, img_size[1]],
    [(img_size[0] / 2 + 55), img_size[1] / 2 + 100]])
dst = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 585, 460      | 320, 0        | 
| 203, 720      | 320, 720      |
| 1127, 720     | 960, 720      |
| 695, 460      | 960, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines # through # in my code in `my_other_file.py`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `yet_another_file.py` in the function `map_lane()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  
