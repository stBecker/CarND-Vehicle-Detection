## Writeup Template
### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/car_not_car.png
[image2]: ./output_images/hog.png
[image3]: ./output_images/sliding_windows.png
[image4]: ./output_images/window1.png
[image4b]: ./output_images/window2.png
[image5]: ./output_images/window3.png
[image6]: ./output_images/window4.png
[video1]: ./output_images/project_video.mp4


[cs1]: ./output_images/color_spaces.png
[cs2]: ./output_images/color_spaces2.png
[cs3]: ./output_images/color_spaces3.png
[cs4]: ./output_images/color_spaces4.png



## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the first code cell of the IPython notebook (or in lines # through # of the file called `some_file.py`).  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]


I plotted the pixel values of random images in different color spaces from the car and not-car classes, to get an idea
which color space would enable a clear distinction between both classes.

![alt text][cs1]
![alt text][cs2]
![alt text][cs3]
![alt text][cs4]


#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters (brute force) and found the parameters `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)` to work the best at correctly
detecting the vehicles in the test images.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using 3-channel HOG, color histograms and spatial binning feature vectors of length 8460.
The code is given in cells 9 and 10 of the notebook.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I decided to search the lower half of the image (i.e. y-coordinates 400-656) with scales 1 and 1.5; searching more scales only
increased the number of false positives without making the detections more reliable. The windows were overlapping 75%
(the window is moved 2 cells per step, with a 8x8 cells window).
The find_cars function is implemented in cell 7 of the notebook.

![alt text][image3]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YUV 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image4]
![alt text][image4b]


To achive a nice result I tried multiple different parameter configurations (e.g. color space, HOG parameters, different window scales etc.).
Evetually I arrived at the current configuration which gave satisfactory results on the test images and the 2 second test video.
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./output_images/project_video.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap
and then thresholded that map to identify vehicle positions.  
To increase the reliability of the detections I combined the heatmaps of the last several video frames into a single
heatmap.
I then used `scipy.ndimage.measurements.label()` to 
identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding 
boxes to cover the area of each blob detected.  

Here's an example result showing the original detections, the labeled boxes and the heatmaps for several test images:

![alt text][image5]

![alt text][image6]



---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  

1) Currently the vehicle detection is very slow (about 1 second per frame) - nowhere near real time detection.
To make pipeline useful for real world applications the performance would need to be improved dramatically. This could be
achieved by either using a different approach than sliding windows (e.g. neural networks) or by making the sliding windows
more efficient (e.g. using multiple CPUs for parallel processing of the windows or using rolling window views).

