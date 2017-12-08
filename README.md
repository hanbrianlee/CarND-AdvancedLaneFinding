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
[image2]: ./output_images/orig_undist.png "Undistorted test image"
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
| 1070, 689     | 1080, 720      |
| 238, 689      | 200, 720      |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image3]
![alt text][image5]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I first started off with udacity lecture's window-sliding method for 2D polynomial fit. I added moving average window scheme with tanh function to smooth out leftx_base and rightx_base where the sliding window searches begin.
The fitting lines function is named `fit_lines()` and the moving average code can be found from line 286~325 in the first code cell of the project ipython notebook. Global variables were used to store previous and moving window arrays.
An example image that illustrates how this is done is shown below (an actual frame captured from the `project_video.mp4`) 

![alt text][image6]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this with two different functions, `get_curvature()` and `get_lanecenter()` located in the first code cell in the project ipython notebook.
I used the following conversion factors between pixels to meters. `get_lanecenter()` function returns an offset compared to the mid-point between the left and right lanes (negative value if the car is offset to the left of the center midpoint)

```python
# Define conversions in x and y from pixels space to meters
ym_per_pix = 30/720 # meters per pixel in y dimension
xm_per_pix = 3.7/700 # meters per pixel in x dimension
```

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step using a function I defined called `save_mask()` which returns an image on which the marked lane is superimposed in green color onto the original image.
This function is located in the first codecell in the project ipython notebook lines 495~517. Using `Minv` acquired from `perspective_transform()` function, unwarped the processed binary warped image.
Here is an example of my result on a test image:

![alt text][image7]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

There were many issues in the beginning. At first I tried only applying S channel of HLS threshold output. This actually worked quite well but as shown in the image below, it failed to process frames in which shadows were introduced or where road colors were suddenly changing. I modified my `process_image()` function so that whenever curvature of the left lane and the right lane differ by more than 2000, the failed frames would be saved into `failed_images` folder so they can be analyzed.
I loaded this failed image to apply different filtering techniques so that my model can properly clear mostly everything else but the lanes. Also, for sanity check, I used previous frame's fit lines' data whenever the curvature deviation between the left and right exceeded 2000. Even with a combination of multiple filtering techniques, for some frames, it was impossible to get a clear lanes-only view for the `fit_lines()` function to do a successful sliding window polynomial fit that will not differ significantly from the previous frame.
One improvement I could make is to incorporate the similar moving window with tanh function smoothing to the 3 parameters of fit_lines arrays for the left and right (`left_fit` and `right_fit`)
Employing this technique should smooth out further on frames where multiple frames consecutively give out erroneous fit lines. (these frames can be seen in my result video with slight blips; but they are not too severe that they deviate from the whole lane marking results)
  
