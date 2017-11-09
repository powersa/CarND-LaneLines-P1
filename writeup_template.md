# **Finding Lane Lines on the Road**

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


Examples are available in /test_images_output/ and /test_videos_output/

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

This pipeline has 6 steps.

1. Grayscale: create a grayscale copy of the input image.

2. Blue: apply a gaussian blur to the image with a kernel size of 3.

3. Edges: apply canny edge detection to the image with a min threshold of 60 and a max of 110.

5. Mask Area of Interest: before applying hough line detection, limit data in the image to an area of interest. I wrote a function (get_mask_vertices) that defines the vertices of this area based on set ratios and the image dimensions. These vertices are then used for the mask.

5. Hough Line Detection: pass the masked image to the hough line detection algorithm. The parameters for this function took a lot of tweaking. *hough_lines* calls the method *draw_lines* to add the vectorized line to an image. I altered *draw_lines* to *get_lane_avgs*, which calculates the average slope and intercept of the right and left lines where right lines have positive slope and left lines have negative slope. The slope and intercept parameters are then used to calculate the coordinate where the lanes intersect with *get_lane_intersect*. I shave 5% off the x coordinate in either direction to get the x-value of the end of the line, from which I compute the y value. To get the other line ends, we compute where the line intercepts the bottom of the image using slope and intercept values. In order to handle cases where we can't calculate valid lanes for a single image, I apply the lines calculated in the previous frame.

6. Apply Lines to Original Image: finally, *weighted_img* annotates the original image with the lanes calculated by previous steps.


### 2. Identify potential shortcomings with your current pipeline


There are several fundamental issues with my existing pipeline.

1. Pipeline parameters are hardcoded for all images. Furthermore, these params took a lot of trial and error to get decent results. That approach is not scalable.

2. Lane detection is currently limited to a single frame at a time. The video would be smoother and perhaps more accurate if lanes were derived from multiple adjacent frames.

3. Along similar lines, the lane lines displayed in the video are a little jittery. It would be relatively easy to smooth line params with something like a moving average.

4. The fault tolerance of this pipeline is relatively low. When I try to run on the challenge, it fails.

5. The heuristic used to distinguish between the right and left lane is pretty naive. Additionally, the lane calculations are quite dependent on a very clean and accurate set of input lines with very little noise or false positives.


### 3. Suggest possible improvements to your pipeline

Looking at the list of problems above, I would first address #4. Identifying and documenting failure cases would help me prioritize the remaining items. I make assumptions in my current code. For example, I must detect a right and a left lane in every image. That may not be possible. If that is the source of failure, I would probably prioritize #2 to take more than a single image into account when deriving the lines.
