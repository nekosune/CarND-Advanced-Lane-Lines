##Writeup Template
###You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

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

[image1]: ./output_images/undist_checkerboard.png "Undistorted"
[image2]: ./output_images/undist_straight_lines1.jpg "Road Transformed"
[image3]: ./output_images/sobel_straight_lines1.jpg "Binary Example"
[image4]: ./output_images/warped_straight_lines1.jpg "Warp Example"
[image5]: ./output_images/final_straight_lines1.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second code cell of the IPython notebook located in "./Advanced Lane Finding.ipynb" 

I prepare an array of object points, of the size 6*9*3 , with z assumed to be 0.

I then loop through all provided images, first turning them greyscale, then running `cv2.findChessboardCorners` to look for the corners, for each image it manages to find them, it first hows the image on screen, for debug purposes, then appends the found corners, and another set of object points, to the appropriate lists.

after each has been run through, it in the third cell, uses `cv2.calibrateCamera` to find the ret, mtx, dist, rvecs and tvecs..

I declare a function here called undistort, that uses those values, and `cv2.undistort` to undistort any image, an example can be given here

![alt text][image1]

###Pipeline (single images)

####1. Provide an example of a distortion-corrected image.
To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
I used a combination of color and gradient thresholds to generate a binary image  as seen in cell 6.
I used the S channel of HLS.

In cell 7, I also use a sobel edge detection to further prepare the image. I did the edge detection on the l channel of the colour space shifted image.

Here's an example of my output for this step.  (note: this is after a perspective transform)

![alt text][image3]

####3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

I define the function `warp(img)` in cell 5 which warps an image with a hard coded set of corrdinates. I chose theese through trial and error, 

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 580,460      | 300,0        | 
| 250,700      | 300,700      |
| 1060,690     | 1000, 700      |
| 700,460      | 1000,0       |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

In cell 10 I have the main bulk of the work.

I use the sliding window technique every frame, due to some difficulty getting the memory based method working.

The provided code, of 9 windows, worked well in the end, and saw no reason to change anything major, with the pixels within the window being added to the list, and the mean of any group above 50 being used to centre the next window.

An example of the image result can be found 

![alt text][image5]

####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this also in cell 10.
I did both left and right curves, , as I found it interesting to see how similar they were to each other, and found it to be a good check and balence to display both.

I also worked out the distance from the centre of the lane, using the basic code of `(right lane X - left lane x) - midpoint`

The resulting output can be seen at the top here

![alt text][image5]

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in cell 10, here is the result of such

![alt text][image5]
---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./output.mp4)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

If I persued this further, I would use the Line class to save information about the last n frames, alongside through obviously incorrect values away, while allowing the values to shift when needed. I would also use techniques from the first lane finding class to provide a better input into the final pipeline step

