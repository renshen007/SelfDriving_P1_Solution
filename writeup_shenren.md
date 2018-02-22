# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 6 steps. First, I converted the images to grayscale, then I applied Gaussian blur to reduce the noise in the grayscale image. After that, a Canny edge detection is applied with a low threshold and high threshold of 50 and 150, respectively. With detected edges, I defined a region-of-interests to lower half of the image. For all the test images/videos, the upper half of the frame does not contain useful information for lane detection. Finally, we applied Hough Transform to the processed image. This will generate a set of lines within the region of interests. Finally, we sort through the lines from Hough transform, and draw two lanes in the original image. 

In order to draw a single line on the left and right lanes, I added draw_lines2() function. In this function, we sort through the lines from Hough Transform based on line slope. One key observation here is that the left lane always has a negative slope, while the right lane always has a positive slope. Based on this observation, we divide the line list into two groups, left_lanes and right_lanes. To merge all left lane elements and right lane elements, we calculate the mean slope and mean intercept in each group. Then two lines are drawn based on the calculated mean slope and mean intercept. One additional implementation detail is that we generated some 'lane memory' using global variables. This is to cope with cases where no proper lanes can be detected in a frame. This was implemented to generate satisfactory results in the 'Challenge' video. 


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when the driver does not follow lanes when driving. Our lane detection pipeline algorithm assumes that the left lane always has a negative slope and resides on the left half of the frame, while the right lane always has a positive slope and resides on the right half of the frame. This hypothesis will break when the driver does not follow lanes when driving.  

Another shortcoming could be our region-of-interest is hard coded into the program thru trial-and-error. All the test images/videos represent cases when the road is relatively flat and lanes are in the lower half of the frame. This situation will change when the road is hilly with lots of ups and downs. Then proper region-of-interest for some frames may not be valid for others. 


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to try to detect region-of-interest based on the spatial frequency and/or color of the image. The upper half of the image is typically the sky, which has a blue color and has little spatial variation. We may do FFT per horizontal line in the image and detect where is the horizon in the image. This should avoid our second shortcoming in the previous section, i.e. hilly road problem. 

Another potential improvement could be to impose maximum variation of lanes in adjacent images. My current implementation only utilizes lane information from previous frame when current frame fails to detect meaning lanes. For driving video, the detected lanes in adjacent images should not vary dramatically. Based on this insight, we may enforce some kind of maximum lane variation for adjacent frames. This can improve the fidelity and stability of lane detection algorithm.  
