# **Finding Lane Lines on the Road** 

## Writeup, Benjamin Pelletier

---

**Finding Lane Lines on the Road**

The goals / steps of this project were the following:
* Make a pipeline that finds lane lines on the road
* Reflect on my work in a written report

See [[P1_Pelletier.ipynb]] for my code.

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consists of the following steups:
1. Convert image to grayscale
    1. I constructed a special grayscale mapping function that assigns "whiteness" and "yellowness" scores to each pixel, and then takes the maximum of the two scores
1. Remove local background brightness
    1. The "background" brightness is determined by the average intensity of the pixels in a rectangular window around each pixel
    1. The background is computed somewhat quickly using a integral image, similar to the Haar approach
1. Identify the minimum and maximum grayscale intensity only within the region of interest
    1. The region of interest is a quadrilateral defined manually that roughly captures the lane as it vanishes to infinity
1. Normalize the entire grayscale image according to the min and max found in the region of interest
    1. The result here should be a grayscale image with as strong edges as possible around transitions to and from lane lines
1. Find Canny edges
1. Mask out all Canny edges outside the region of interest
1. Find line segments in the Canny edge image using the Hough transform
1. Classify each line segment as "left lane line", "right lane line", or "neither" according to slope and position on the image
    1. Line segments classified as "left lane line" are annotated with wide green lines
    1. Line segments classified as "right lane line" are annotated with wide blue lines
1. Using linear regression on the endpoints of the line segments assigned to a particular lane line, weighted by line segment length, to find the single best-fit line for each particular lane line
1. Remove outlying endpoints and repeat the previous step and this step until there are no outliers
1. Annotate the best-fit line for each lane line in red

The output of the pipeline can be best seen in the videos in the test_videos_output folder in this repository (see above for index).

Please see also that the challenge video in that folder is annotated fairly well.

### 2. and 3. Identify potential shortcomings with your current pipeline, and Suggest possible improvements to your pipeline

Each shortcoming mentioned below is then followed by a suggestion for possible improvement.

* Lane lines are assumed to be straight; this will fail when the road curves appreciably
    * My regression approach could be changed to fit against the curve that is formed from a perspective projection of a circle (implicitly assumes roads locally follow clean circular arcs, which seems like a good approximation)
* The region of interest is hard-coded, so a shift in the camera's position on the car or orientation will result in poor performance
    * There could be a calibration phase where constraints are relaxed and the region of interest is automatically computed based on a reasonable set of training data
* Rejection of disjoint line segments is poor (see the bridge crossing in the annotated challenge video)
    * Detected line segments could be rejected as outliers if the line segment they represented is not sufficiently coincident with the best-fit line segment
* Similar to the previous point, more generally, the context of the line segment that the line segment endpoints belong to is ignored when regressing the best-fit lane line
    * If this context was used, performance could be improved
* Computational speed is slower than I'd like
    * There are a few places in the code where I stuck with code clarity rather than optimizing for performance.  This could be changed for production.


