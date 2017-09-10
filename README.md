
**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a computer vision based pipeline that finds lane lines on the road

[//]: # (Image References)
[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Description of the pipeline

The pipeline consists of the following steps:

1. Convert the image to gray scale
2. Detect the edges using the canny edge detector
3. Detect the lines in the edge image using Hough transform
4. Combine the lines on the left and right sides to produce one single line for each side.
5. Robust prediction of the lines for each frame

In order to draw a single line on the left and right lanes, a new function called draw_lines_roi() was created. Inside this function, all the lines detected from the hough transform are processed one by one. They are assigned to the left or the right lane based on the slope. In order to reject spurious lines and lines that are not in the expected direction, we use limits on the slope to reject unwatned lines. For each line that is accepted, we extrapolate the line to the edges of the ROI and store the end point coordinates. Once all the detected lines are processed like this,  the end points for the left and right lanes for that frame are compputed by combining all the individual end points. We use the median instead of the mean here to make the averaging more robust to outlier points. Further robustness is built in by handling two conditions descried below:  

1. If no detected line from the hough transform is accepted to be part of the left or right lane for a particular frame (or if no line was detected), we use the line from the previous frame as the predicted lane for the curent frame. This helps to handle frames that have difficult conditions to detect the lines (as in the challenge video). 

2. If the end points of the final line for each lane moves significantly from the previous frame, we use the line from the previous frame as the predicted line for the current frame. This helps to handle cases where the line "jumps" from one frame to other due to spurious detections.

### 2. Potential shortcomings with the current pipeline

There are couple of potential shortcoming in the current pipeline. If there is a sudden substantial change in the location of the lanes (maybe due to the car swerving), the algorithm will not be able to jump to the new location due to the constraint on how much the line can move between frames. Another potential shortcoming is the ability of the feature detector to handle wide variation in lighting conditions.

### 3. Possible improvements to the pipeline

One possible improvement to the pipeline would be to improve the feature detector to handle illumination changes. For example using adaptive thresholds for the feature detection based on information from the frame itself. Another potential improvement is to make the lane prediction more robust by weighting the information from the current frame and previous frames to smooth the lane locations as well as to help regain track even if there are suddeng jumps.
