# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image0]: ./examples/grayscale.jpg "Grayscale"
[image1]: ./Report materials/gray.png "Grayscale"
[image2]: ./Report materials/blur.png "Gaussian blur"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

At first there was a lot of trial and error to get the functions working on after another. After all the functions
were tested I started to work on the end goal.

1. Firstly I read in the image and print its parameters to know what I am working with.
Example result: Image solidWhiteCurve.jpg is:  <class 'numpy.ndarray'>  with dimensions: 	 (540, 960, 3)
2. Next, I convert it to grayscale
![alt text][image1]
3. After that image is blurred with gaussian blur to filter out some noise
![alt text][image2]
4. Then the edges of objects are found using function canny. For this function the thresholds were taken
at first from the course tutorials (50 and 150) and they worked well. To fine tune them I changed one at a time
to get a feeling what would work the best. Moving the low threshold to 75 didn't have any impact on the lane
lines, but the background lines were cropped. From the tutorials it was suggested to keep the ratio in between
1:2 and 1:3 so I tried the high threshold up to 225 and it started to crop out some of the lane lines so I used
200 instead.
5. Next up I masked the image to only get edges from the laneline regions and filter out everything else. As I 
had printed out the image parameters earlier I knew the image dimensions and accordingly adjusted the 4 corners 
of the region in interest.
6. Afer that the edges were converted to line segments which should represent the lane lines on the road. To do so
the hough lines algorithm was used. It took the edges from canny edges only from the region of interest and combined
them to lane line segments. The parameter values were taken from the experimented values from the Computer Vision
Fundamentals lesson and as they worked pretty much out of the box they were left unchanged.
7. Finally, the found lines and original image were blended to get the final result


In order to draw a single line on the left and right lanes, I modified the draw_lines() function by averaging all
the lane line segments to get the general line to represent the lane car is in. In order to do so, I found the 
average left and right lane slope and bias and built the new line based on these parameters. The y min and max
were selected so that the lane lines would be in the bottom of the image and in the middle of the image and based
on these parameters the rest of the lane line parameters were deducted so the left and right lane lines could be 
drawn. In order to make the algorithm work a bit bitter after the intial pipeline was working I printed out the 
slopes of lines in order to see what is the magnitude of the lines and added voter in the range of 0.5 and 0.9 so
if the slope is in this range the parameters of the line are added to slope and bias arrays.

If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image0]


### 2. Identify potential shortcomings with your current pipeline

One potential shortcoming would be what would happen when there are roadworks up ahead and the lane lines
are placed more narrow than usual so there would be more lines in the masked region. 

Another shortcoming could be in case there is a car driving right in front of you. The field of view would
be quite limited and there could be some lane line kind of lines from the front car which would be disrupting 
the actual readings.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to average the lane lines over couple of frames to make them more stabile.

Another potential improvement could be to check where the last line was positioned and based on that vote 
if the new line position and angle is reasonable or it is way out of place and is not considered a valid
line.

Also colours could be of help. Not in the RGB colourspace, but in HSV or some other colourspace it would be 
possible to detect potential lane lines.
