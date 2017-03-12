## Advanced Lane Finding Project Writeup
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/car_not_car.png
[image2]: ./output_images/HOG_example.png
[image3]: ./output_images/sliding_window.png
[image4]: ./output_images/heat_map.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

###Histogram of Oriented Gradients (HOG)

####1. HOG Features Extraction

I started by reading in all the vehicle and non-vehicle images in the first code cell of the IPython notebook. Here is an example of one of each of the vehicle and non-vehicle classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `RGB` color space and HOG parameters of `orientations=6`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`in cell 4 of the IPython Notebook:

![alt text][image2]

####2. Choosing HOG Features

I tried various combinations of HOG parameters and color spaces, and it was interesting to see how the classifier accuracy changed with the different combinations. After getting an classification accuracy between 99-100%, I settled on the following parameters and color space ( Final values were used in the final cell of the IPython Notebook ):

| Parameter              | Value   | 
|:----------------------:|:-------:| 
| Color Space            | YCrCb   | 
| orientations           | 9       |
| pixels_per_cell        | (8,8)   |
| cells_per_block        | (2,2)   |
| hog_channels           | 'ALL'   |
| spatial_size           | (32,32) |
| histogram_bins         | 32      |

####3. Training a Linear SVM Classifier

I trained a linear SVM using the selected HOG features along with the histogram of the color channels and the spatial binning of the colors. The code for training can be found in cell 5 of the IPython Notebook.

###Sliding Window Search

####1. Sliding Window Search Implementation

For the searching the images for cars, I would extract the hog-features of the whole image and then sub-sample that image into smaller images and used the trained classifier to find cars. This proved to be much faster than extracting windows from the original image and extracting the hog-features for each of the windows. The code for applying this technique is in cell 8 of the IPython notebook.

Ultimately I searched on three scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result. It is worth mentioning that I used a different vertical search area for each scale to minimize the processing time. Also, it made sense to only search the lower half of the image for cars. Here are some example images:

![alt text][image3]
---

### Video Implementation

####1. Applying Lane and Vehicle Detection

I used the ouput of the previous project and applied vehcile detection to have a pipeline that detects the lane and cars.

Here's a [link to my video result](https://youtu.be/JvMm-sQcA2U)


####2. Removing False Positives and Combining Bounding Boxes

In order to remove false positives, I recorded the positions of positive detections over 10 consecutive video frames. From the positive detections of these 10 frames, I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected. I used an OOP approach to be able to keep track of previous frames. This can be found in the second to last cell of the IPyhton Notebook.

Here's an example result showing the heatmap from a series of frames of video, the `scipy.ndimage.measurements.label()` method was used to create a bounding box to cover the area of each detected blob:

![alt text][image4]

---

###Discussion

At the beginging of the project, I was using the RGB color channels to extract hog-features. The accuracy was not that good, so I had to try other color spaces. The 'YCrCb' color space proved to work best. Processing the video with three different window scales proved to be taking about an hour on my laptop. The pipeline might fail if the visibility conditions were as clear as they were in the video, so for detecting vehicles at night, other techniques might need to be considered. The pipeline would be more robust if for example an object is created for each detected car and would keep track of that car in case it went behind another car.

