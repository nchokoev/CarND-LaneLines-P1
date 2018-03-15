# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/in_image.png
[image2]: ./examples/yellow_mask.png
[image3]: ./examples/white_mask.png
[image4]: ./examples/white_yellow_mask.png
[image5]: ./examples/edges.png
[image6]: ./examples/lines_edges.png
[image7]: ./examples/w_img.png
[image8]: ./examples/final_img.png

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps.
#### 1. Read the input image
![alt text][image1]

#### 2. Mask all white and yellow segments on the image. This way I separate the lane lines more clearly, especially in areas with shadows or glare (as in the 'challenge.mp4' video). The mask done in two steps:
##### 1. Mask the yellow color in the image - convert the image in HSV space to separate the color information from the brightness easily. Then filter the yellow color using 'cv2.inRange' and providing the upper and lower HSV values for the yellow range.
![alt text][image2]
##### 2. Mask the white color in the image - this is better done in the RGB space as the white /gray shade/ can be represented as equal values of Red, Green and Blue.
![alt text][image3]

The end mas image is produced by bitwise or-ing both, white nad yellow masks
![alt text][image4]
#### 3. Compute the edges over the mask image using canny edge detection.
![alt text][image5]
#### 4. Select a region of interest in the image around the position lanes segments. The region of interest in my case is trapezoid described by:
- WL1 = 0.84*X - the length of the bottom base /width/
- HL1 = 0.4*Y - the height
- C1x = 0.54*X - the center of the trapezoid on X axis
- Sl1 = 0.7 - the slope of the left side
- Sl2 = 0.6 - the slope of the right side

X and Y are the dimensions of the image in pixels
![alt text][image6]

#### 5. In the region of interest compute the lines using the Hough Transform (cv2.HoughLinesP()). 
![alt text][image7]

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by:
##### 1. Store each line and its slope to lists. Store left and right lines and slopes to different lists. 
##### 2. Stack vertically all the lines for each side (np.vstack()) separately for the left and the right lane in order to find the most upper point coordinates (where the line ends).
##### 3. Calculate the mean value for each side slope.
##### 4. Draw a line from the upper point to the bottom of the image with the calculated mean slope
![alt text][image8]

### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when there are other markings on the road between the lines. 

Another shortcoming could be lines on sharp curves.

It will be interesting to see how it behaves in different light conditions (sunset, sunrise, night, artificial lights,...)


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to define a region of interest excluding the middle part for lane detection.

Another potential improvement could be to tweak the color separation and edge detection.
