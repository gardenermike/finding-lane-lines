# **Finding Lane Lines on the Road** 

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. My image pipeline

My pipeline is 6-step process:

1. Convert the image to grayscale.

2. Apply a Gaussian filter to the image to remove noisy boundaries.

3. Use a Canny filter to find edges.

4. Mask off a trapezoidal area covering the area where lane lines are likely to be in the image.

5. Use a Hough filter to select likely straight lines.

6. Processes the possible lane lines into a single left and right lane line.

Step 6 is the most interesting part, and deserves elaboration. It is implemented in the draw_lines function.
I first group all of the matching lines from the Hough filter into those with positive and negative slopes.
Presumably, these would represent the left and right lane lines, respectively.
I then perform a linear regression on all of the endpoints of the lines in the left- and right-lane groups.
The linear regression gives me the equations of two lines. I then find the intersection of those two lines,
and the intersection of each line with the lower boundary of the image. I consider the resulting triangle
the lane.
I draw a thick red line along each line, and color the center blue to add clarity.

In order to support noisy videos, I added an important change. I keep a running average of the slope and y-intercept
of each presumed lane line. Each new image only contributes 1% to the running average. This aggressive smoothing
weeds out the effect of noisy outliers, while still allowing for gradual changes like corners.


### 2. Potential shortcomings

I noticed that a bad outlier lane line estimation at the beginning of a video can have an outsized effect. I considered mitigating
that effect by starting with higher weight in the moving average that would decay over the first few seconds. In the end I tweaked
my masked area a little for the challenge video.

Lane lines wildly outside of the norm, such as perpendicular lines ahead while crossing a street, would be totally missed.

The barricade on the left in the challenge video represented a real challenge as well: it was a clear line similar to the left lane line.
If the image was not well-centered, similar boundaries could throw off the lane calculation.

### 3. Possible improvements

I would like to further explore weighting the lines from the Hough filter by length. I believe that such a weighting would
tend to discard noisy non-lane line lines in the image. I think that looking for parallel and nearby lines would also
preferentially select for lane lines instead of other lines, like concrete barricades.
Fine-tuning of the mask could also help. I experimented with removing a center triangle, without much success.
More exhaustive exploration of that approach would likely yield results.
