#**Finding Lane Lines on the Road** 

##Pipeline

We will trace through the pipeline with this example image
![Original](original.png =300x)

1. Convert the image to grayscale, and select a region of interest.  We select
a trapezoidal region roughly following the expected slope of the lane lines and excluding the part of the image above the horizon.  Here is an example image after this step.

    ![Step 1](P1.png =300x)

2. In the second step, we blur the image with a Gaussian filter to remove noise.  We chose a kernel of size 5.  We then threshold the image, setting all pixels of intensity less than 150 to black, and those greater than 150 to white. This 
creates a black and white image.

    ![Step 2](P2.png =300x)

3. In the third step we apply the Canny edge detection algorithm to the thresholded image.  We found this step to be fairly insensitive to the setting of the low and high threshold parameters in the Canny edge detection algorithm.  Ultimately, we set the low threshold to 1 and the high threshold to 5.
    
    ![Step 3](P3.png =300x)
    
4. In the fourth step, we apply the probabilistic Hough line detection algorithm to extract lines out of the edges we found in step 3.  We found that errant lines were not too big of a problem in this step, and the more difficult case was getting enough signal out of the lane lines.  Thus we chose a low threshold of 10 and set the min line length to 1. For this part of the project we set the maximum line gap parameter to 10.  This leaves striped lanes unconnected, as in the target examples. 

    ![Step 4](P4.png =300x)
    
5. Finally, we overlay the lines obtained from the Hough transform onto the original image.

    ![Step 5](overlay.png =300x)
    
Here are the results of the pipeline on the other example images.

![Solid White Curve](overlaysolidWhiteCurve.jpg =300x)
![Solid White Right](overlaysolidWhiteRight.jpg =300x)
![Solid Yellow Curve](overlaysolidYellowCurve.jpg =300x)
![Solid Yellow Curve 2](overlaysolidYellowCurve2.jpg =300x)
![White Car Lane Switch](overlaywhiteCarLaneSwitch.jpg =300x)


## Extending Lane Lines
For the second part of the project, we took the line segments identified by the Hough algorithm and extrapolated from them a single composite line showing the full extent of the lane on the left and right hand side.  The basic pipeline remained the same as above with one key difference.  We now set the maximum line gap parameter in the Hough line detection algorithm to be 200.  This already had the effect of connecting many partial line segments together, and reduced the number of lines returned by the Hough algorithm.  

Given the (x1,y1), (x2,y2) endpoints of lines returned by the Hough algorithm, we then separated them as belonging to the left or right lane line base on their x coordinates.  Then for each of the left set and the right set we found the line of best fit in the least squares sense.  We also looked at weighting points by the length of the line they lie on and using weighted least squares.  This appears to give a slightly more stable line.


## Reflection


### Potential Shortcomings
The current pipeline has some parameters that were manually set to work with the 
example images.  For example, in the thresholding step we assume that the asphalt will be of a dark color so that we can use intensity thresholding to distinguish the lane lines from the road.  This step would fail on sections of road that are much lighter in color, as in the challenge video.

We also manually set the region of interest, which assumes that the road ahead is straight.  Around curves, this selection would exclude relevant parts of the lane lines.


### Possible improvements
It would be nice to adaptively set parameters to adjust for different conditions.  For example, one could do some preliminary processing to determine a probable region of interest, rather than hard coding the region of interest.  Also the thresholding value could be adaptively determined to handle changes in road color.
