
# **Vehicle Detection**

[//]: # (Image References)

[car]: ./output_images/car_class.png "car"
[ncar]: ./output_images/ncar_class.png "ncar"
[hog_gray]: ./output_images/hog_gray.png "hog_gray"
[find_cars]: ./output_images/find_cars.png "find_cars"
[find_cars_b]: ./output_images/find_cars_b.png "find_cars_b"
[heat_img]: ./output_images/heat_img.png "heat_img"
[detection]: ./output_images/detection.png "detection"
[False-single-detection]: ./output_images/False-single-detection.jpg "False-single-detection"



### Submission Files/Writeup

You're reading the writeup! and here is my [project code](https://github.com/purnendu23/Vehicle-Detection/blob/master/vehicle_detection.ipynb)

### Histogram of Oriented Gradients (HOG)

#### 1. Extracting HOG features from the training images.

I started by reading in all the `cars` and `non-cars` images (cell [4]).  Here is an example of one of each of the `cars` and `non-cars` classes:
![car]
![ncar]

I also extract some more images from `project_video.mp4` so that i can test on those images also (cell [13])

I define `get_hog_features` in cell[4] which calls the `hog` function from the `skimage.feature` library to calculate the hog features of an image. I test this on a gray scale image(cell[5]). Cell[9] has the code for `extract_features` method which is used to extract features from all the images.

Here is an example using the `gray` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(16, 16)` and `cells_per_block=(2, 2)`:

![hog_gray]

#### 2. Finalizing HOG parameters.

I tried various combinations of parameters and finalized on the following:
`
c_space = 'YUV'
orient = 11
pix_per_cell = 16
cell_per_block = 2
hog_channel = 'ALL'
`
#### 3. Training the classifier using the selected features.

I train a linear SVM classifier (cell [31]). I try various features to train the classifier on by modifying the color space and channels above and found best accuracy with c_space = `YUV` and hog_channel = `ALL`. I also try to get more data by augmenting my training examples (cell [33,2]) with translated and rotated images but it does not make much diffrence in the accuracy or even on the final video. So, that is left out.

### Sliding Window Search

#### 1. Implementation

I define the `find_cars` function (cell [9]) to implement the sliding window approach.
Cell[8] has the `draw_boxes` function which is used to draw the boxes on the image and check the scope of `ystart` and `ystop` and the effect of changin the `scale`. Here is the output from the sliding window approach:

![find_cars]  ![find_cars_b]

#### 2. Handling false positives and method for combining overlapping bounding boxes.

I first define `add_heat`, `apply_threshold`, `draw_labeled_bboxes` in cell[16] to tackle the false positives. I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example :
![heat_img]

### 3. Here are the resulting bounding boxes on all test images:

I test the pipeline at this stage in cell[17, 18] and here is the result on all the test images: 

![detection]


### Video Implementation

#### 1. Final pipeline

I add the `Box_list` class to hold the boxes detected in the older video frames and redefine my final pipeline `findCarsInVFrame` to accomodate this change. The threshold limit changes here to account for all the boxes detected and stored in the Box_list object.

#### 2. Final project video

Here's a [link to my video result](./project_video_out.mp4)

---

### Discussion

#### 1. Problems / issues faced in the implementation of this project.  Where will the pipeline likely fail?  Improvements?

* **Jittery updation:** The bounding boxes updation around the vehicle is jittery. I am not yet sure how to make that smoother. Perhaps, tracking 

* **False positives:** I beleive there are three ways to get rid of false positives.
1. One, use a better classifer. I intend to use a neural network next to see if I can improve the accuracy. 
2. Second is to use a better choice of parameters for sliding window approach (ystart, ystop and scale) which is mostly trial and error.
3. Define a new threshold which is also mostly trial n error.

I already play around with #2, #3 in this implementation to reduce the false positives in the final project video output

* **False-single-detection:** The following figure from the final output video shows this problem. It is not a very big problem though.
![False-single-detection]
