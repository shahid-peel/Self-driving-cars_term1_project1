**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"
[image2]: ./examples/grayscale_blur.jpg "Grayscale with Blur"
[image3]: ./examples/grayscale_blur_edges.jpg "edges detected from grayscale blurred image"
[image4]: ./examples/grayscale_blur_edges_roi.jpg "Region on interest on edges"
[image5]: ./examples/grayscale_blur_edges_roi_lines.jpg "Lines extracted from edges in roi"
[image6]: ./examples/grayscale_blur_edges_roi_lines_combined.jpg "Final output"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 6 steps. First, I converted the images to grayscale.  Then I applied gausian blur with a 5x5 kernel.  Next I applied the canny edge detector on the blurred grayscale image which results in edges.  I used a low threshold of 150 and 200 for high threshold (found these by empirical analysis).

Next I clipped the edges images according to a region of interest polygon (I used a Isosceles trapezoid shape).  On the clipped edges I applied hough lines helper funciton (which I modified internally for averaging out lines).  Finally I merged the resultant output from hough lines with the original image, thus giving an overlay of lines (red) over original image.  This can be easily extended to videos by repeating the process for every frame in the video.

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by doing five things:
1.  Spliting lines into a 'likely left' and 'likely right' bucket
2.  Ignoring lines that do not conform to the general properties of the left and right line (using range of y-intercepts)
3.  Averaging out the co-efficients (slope and y-intercept) of the lines in the left and right buckets
4.  Finding the intercepting x coordinates of the top and bottom edges of the clipping region
4.  Displaying the resulting lines, one for the right and one for the left


Here are the images showing how the pipeline works: 

![alt text][image1]
![alt text][image2]
![alt text][image3]
![alt text][image4]
![alt text][image5]
![alt text][image6]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when the car would be switching lanes and the car would not be looking straight down the road i.e. what if it is at an angle.  My filtering of outliers edge lines based on y-intercept might not hold true in that case.  A probabilistic solution might be more robust, one which doesn't rely on hard set thresholds of line co-efficients. 

Another shortcoming could be that here no information is flowing from one frame to the next, i.e. I am not maintaining any state. For a more robust solution, every frame should add some information to the currently running understanding of the world, with most weight being given to the newest information/frame.  


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to have some information flowing from one frame to the next. I allued to it a bit in #2 above. 

Another potential improvement could be to not have any hard coded parameters/threshold values in the program.  The program should work just as well at night.  So the program can take the first few frames until it has an idea about the parameters value that it should threshold on, and even then those values should change over time as the driving conditions change.
