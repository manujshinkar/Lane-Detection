# Lane Detection Project

Introduction
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

[//]: # (Image References)

[image1]: ./images/undistort_output.png "Undistorted"
[image2]: ./images/undistorted.png "Road Transformed"
[image3]: ./images/gradient_threshold.png "Binary Example"
[image4]: ./images/warped.png "Warp Example"
[image5]: ./images/threshold_warped.png "Thresholded Warped"
[image6]: ./images/histogram.png "Histogram"
[image7]: ./images/sliding_window.png "Sliding Window"
[image8]: ./images/previous_polyfit.png "Previous Polyfit"
[image9]: ./images/lane_information.png "Lane Information"
[video1]: ./project_video_output.mp4 "Video"

Camera Calibration
---

The code for this step is contained in the first and second code cell of the IPython notebook lane_detection.ipynb.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### Example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### Example of gradient and thresholded binary image

I used a combination of color and gradient thresholds to generate a binary image in cell 6 of lane_detection.ipynb.  Here's an example of my output for this step. 

![alt text][image3]

#### Example of Perspective Transform

The code for my perspective transform includes a function called `warper()`, which appears in the 4th code cell of the IPython notebook.  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

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

#### Finding lane line pixels and fit a polynomial 

The code for this is in cell 10th of lane_detection.ipynb

##### Thresholded warped image
![alt text][image5]

##### Histogram of pixels
![alt text][image6]

##### Lane line detection using sliding window
![alt text][image7]

##### Lane line detection on new image using information from previous frame
![alt text][image8]

#### Calculation the radius of curvature of the lane and the position of the vehicle with respect to center

I did this in cell 14 of lane_detection.ipynb

#### Example image of the result plotted back down onto the road such that the lane area is identified clearly

I implemented this step in cell 16.  Here is an example of my result on a test image:

![alt text][image9]

---

### Pipeline (video)

Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

In this project the most critical part was to fetch lane infomation from a given image under different light conditions. I initially tried using gradient on image which were in RGB color space. I was loosing some lanes in test images. Later I tried to explore the HSV and HLS color spaces. I foound out that the lanes are sumch more visoble in S channel in the HSV color space. In the final implementation I used a combination of gradient and color thresholding to get the best results in different light conditions. I did not test my implemetation in very dark light conditions in which I expect my implementation to fail. 

After getting the lane pixels, I use a second degree polynmial to fit and get lane lines. I could use a cubic or higher degree polynomial to get better results.

If I were to implement this in a real car, I would try to get longer lane lines. I would also try to use data from mpas so that I can see futher ahead.


