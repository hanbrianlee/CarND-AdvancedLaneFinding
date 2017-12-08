**Advanced Lane Finding Project**

**BRIAN 'HAN UL' LEE

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
[image2]: ./output_images/undist_straight_lines1.jpg "Undistorted test image"
[image3]: ./output_images/orig_warped.png "Road Transformed"
[image4]: ./output_images/orig_filteredFinal.png "Binary Example"
[image5]: ./output_images/orig_finalFilteredWarpedBinary.png "Warp Example"
[image6]: ./output_images/plot_fit_lines_windows.png "Fit Visual"
[image7]: ./output_images/orig_finalMaskFiltered.png "Output"
[video1]: ./result.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the `caliberate_camera()` function in the first code cell of the IPython notebook located in `project.ipynb`. This cell is where all the module imports and function definitions are made. The following describes the method taught in udacity's lectures.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the one of the test images using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of HLS, HSV, gradient thresholds, white and yellow color extraction to generate a binary image. The steps are laid out in a function called `filter()` in the first code cell in `project.ipynb` and there are several other helper functions within the same code cell that aids the thresholding and combination process. Here's an example of my output for this step. 

![alt text][image4]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `perspective_transform()`, which appears in the first code cell in the file `project.ipynb`. The function takes as inputs an image (`img`) and outputs the warped image. I chose to hardcode the source and destination points in the following manner:

```python
src = np.float32([[570,465],[712,465],[1070,689],[238,689]])
    
# For destination points, pick out a good four rectangular corner points, with enough offset so that curved lines won't get cut off
offset = 200 # horizontal offset for dst points    
#corner designations in dst go in clockwise direction starting from the left top corner
dst = np.float32([[offset, 0], [img_size[0]-offset, 0], [img_size[0]-offset, img_size[1]], [offset, img_size[1]]])
```

The above src points were chosen from the undistorted version of straight_lines1.jpg image opened up in mspaint so that my mouse cursor can tell me which pixels my cursor lies on. I chose the points so that the front hood is not within the 4 corners I chose, and also that the outer parts of the lanes were used to pick the corners.

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 570, 465      | 200, 0        | 
| 712, 465      | 1080, 0      |
| 1070, 689     | 760, 720      |
| 238, 689      | 200, 720      |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image3]
![alt text][image5]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image6]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines # through # in my code in `my_other_file.py`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `yet_another_file.py` in the function `map_lane()`.  Here is an example of my result on a test image:

![alt text][image7]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  
