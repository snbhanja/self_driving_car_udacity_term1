# **Finding Lane Lines on the Road** 

----
[//]: # (Image References)

[input_image]: ./examples/1.png "Input image"
[grayscale]: ./examples/2.png "Grayscaled image"
[gaussian_blur]: ./examples/3.png "Gaussian blurred image"
[canny_edge]: ./examples/4.png "Canny edge detection"
[roi]: ./examples/5.png "Set region of interest"
[hough_lines]: ./examples/6.png "Hough lines"
[final_image]: ./examples/7.png "Lanes detected"
[not_working]: ./examples/not_working_output.jpg "Left lane not detected properly"

### Reflection

Pipeline consisted of 7 following steps:
* (1) Convert image to grayscale
* (2) Blur it with the chosen kernel
* (3) Apply canny edge detection to obtain edges with the chosen high and low thresholds
* (4) Set vertices of the region of interest
* (5) Apply an image mask according to chosen set of region of interest coordinates
* (6) Obtain a list of hough lines within a region of interest according to given set of hyperparameters and plot them into a blank image (all black)
* (7) Fuse image containing those hough lines with the original image

In order to draw a single line on the left and right lanes, I modified the draw_lines() function the following way:

* All obtained hough lines being separated into two heaps denoting left and right lanes according to their slope ('m' in y = mx + b equation). As vertical axis increases from top to down, negative slopes go to the left heap, positive - to the right.
* Average slopes and intercepts (m's and b's in y=mx+b) within each heap. It yields data for 2 linear functions at maximum (one for left and one right lane)
* compute coordinates of intersection with the button and the upper edges of the region of interest according to obtained functions.
* Draw those averaged lines accross the full extent of the region of interest

The step step by image output as below.
1. Input Image: 
![alt text][input_image]

2. Grayscaled Image: 
![alt text][grayscale]

3. Blurred Image: 
![alt text][gaussian_blur]

4. Canny edge detection: 
![alt text][canny_edge]

5. Region of interest: 
![alt text][roi]

6. Hough Lines : 
![alt text][hough_lines]

7. Lanes Detected: 
![alt text][final_image]

###2. Potential shortcomings

Potential shortcomings are mostly related to detection of false positives and possible lack of true positives.

The example of the latter can be seen at the following images:

![alt text][not_working]

Here we can see that canny function fails to detect the left lane while it is definitely present within the original image.

Another possible shortcoming is that averaged slopes and intercepts implicitly contain values of all lines participating in averaging and it can possibly lead to not exactly following the lanes markings.

###3. Possible improvements

An improvement for better performance with false positives / true positives would probably be to better tune the hyperparameters: kernel, thresholds, minimum line length, maximum line gap etc. But there is a danger to 'overfit' for some particular image class which would ultimately affect the overall performance.
In my opinion, there is always a tradeoff, and, after all, it is probably better not to detect the existing lane line at all rather then falsely detect the non-existing one.

To address the average lines not exactly following the actual lanes probably requires some more sophisticated averaging techniques, maybe involving operations across the neighbouring frames.

###4. Optional Challenge

To improve detection I've implemented two techniques:

* filtering out of lines with too steep and too gradual slopes, removing mostly vertical and horizontal lines, which are of no interest for the task
* among those remaining after the previous step, filter out the outliers to eliminate their influence on averaging.

These operations are being applied within a draw_single_line() function which is in turn being called from my implementation of draw_line(). Operations are actually implemented at the following functions:

* reject_outliers()
* reject_extremes()
* remove_outlier_slopes()
* remove_extreme_slopes()
* filter_by_mask()

The repository contains the resulting video of an optional challenge as 'extra.mp4'
