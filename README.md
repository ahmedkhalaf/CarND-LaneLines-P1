# **Finding Lane Lines on the Road** 

## Writeup for Udacity Self-Driving Car Nano-degree Project1

### (CarND-LaneLines-P1)

### By: Ahmed Khalaf


---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./writeup_imgs/challenge_over_filtered.png "Noise filter removed features"
[image2]: ./writeup_imgs/challenge_noise.png "Noise resulting in wrong detection"
[image3]: ./writeup_imgs/Horizon_Noise.png "Noise near horizon"
[image4]: ./writeup_imgs/Lines_Detected.png "Lane lines deteced"
[image5]: ./writeup_imgs/vlcsnap-2018-05-12-20h42m38s53.png "Visual guides"

---

### Reflection

The pipeline consisted of 6 steps, split into three phases.

### A. Pre-processing
#### A.1 Conversion to Grayscale


#### A.2 Noise filtering (using Gaussian Smoothing)

Finding the good balance depends on how the next steps can tolerate noise:
* Removing lines resulting from tree shadows, cracks in the ground ..etc
* However, it can also remove useful features like washed out lane lines (really important for challenge.mp4)

![alt text][image1]
![alt text][image2]


### B. Detecting Lanes
#### B.1 Edge detection and Masking
Edge detection is done using canny function, threshold 50-150 works in most cases where lanes are washed (like in challenge.mp4), however, this introduces more noise.

Therefore, Choosing a mask below horizon line plays an important role in removing a lot of objects near the perspective convergence point.

![alt text][image3]

The chosen mask represents a trapezoid which high side is not wide and is located below horizon.

#### B.2 Line detection
Detecting lines is done in relatively high-resolution hough space:
* rho = 2 # distance resolution in pixels of the Hough grid
* theta = np.pi/180 # angular resolution in radians of the Hough grid

Looking for longer lines by increasing threshold and at the same time tolerating gaps helps accurately detecting lanes, since these two features distinguish lanes after edge-detection phase:
* threshold = 80     # minimum number of votes (intersections in Hough grid cell)
* min_line_len = 20 #minimum number of pixels making up a line
* max_line_gap = 50    # maximum gap in pixels between connectable line segments

![alt text][image4]

### C. Visual Annotation
In order to draw a single line on the left and right lanes, I modified the draw_lines() function by attempting to find two lines with max slope (closest to the car's trajectory which is usually straight line).
When these are identified, intersection points to horizontal upper and lower lines of the interest area are calculated based on equation of the identified lines.

I also modified default thickness of the line to 12 pixels.

An example is shown below, it shows that noise resulting from tree shadows is eliminated

![alt text][image5]


### 2. Potential areas of improvement in current pipeline implementation

The current implementation assumes Horizon is near the middle of captured image, this will probably not work in Pennsylvania for example where roads are not horizontal and horizon will change significantly.

In addition, the implementation assumes Car is within lane .. this will not be useful in situations where the car departures the lane

It's also assumed that the lanes are relatively straight .. which isn't expected in situation where road is curved like highway exits and parking buildings.

Stateless:
* Each frame is interpreted without making use of information from previous frames.
* Unable removing static noise (like in challenge.mp4)

### 3. Furture work

Horizon could be detected dynamically by attempting to find horizontal line that is near to significant change in color compared to upper part of the image.

Performance can be enhanced by removing part of the image which doesn't need to be processed.

To make use of lanes detected in previous frames to overcome noise or lanes not being visible on the road in addition to removing static noise.

## Final Deliverables:
https://github.com/ahmedkhalaf/CarND-LaneLines-P1/blob/master/test_videos_output_final/solidYellowLeft.mp4
https://github.com/ahmedkhalaf/CarND-LaneLines-P1/blob/master/test_videos_output_final/solidWhiteRight.mp4
https://github.com/ahmedkhalaf/CarND-LaneLines-P1/blob/master/test_videos_output_final/challenge.mp4

## Intermediate Deliverables (same videos with line-segments)
https://github.com/ahmedkhalaf/CarND-LaneLines-P1/blob/master/test_videos_output_line-segments/
