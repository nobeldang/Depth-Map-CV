# Depth Map

### Objective: <hr>

Using a single camera it is not possible to estimate the distance of point P from the camera located at point O. All of the points in the projective line that P belongs to will map to the same points p in the image. Therefore, making it impossible to estimate the distance.

![image](https://user-images.githubusercontent.com/26826339/191177609-46b2a8bf-9a58-4c73-8095-8249dc93d0dd.png)


However there is a solution to this problem; a stereo camera system can be used. Therefore, here I will explore how we can use a parallel camera system to estimate depth of objects in an image from scratch, without using any libraries.

![image](https://user-images.githubusercontent.com/26826339/191183474-bf84adb3-e6b5-476b-a42a-86dcd6928782.png)

### Calculating depth(z): <hr>

                      𝑑𝑖𝑠𝑝𝑎𝑟𝑖𝑡𝑦 (𝑑) = (𝑥𝑙 − 𝑥𝑟)   - (1) <br>
                      𝑑𝑒𝑝𝑡ℎ (𝑧) = 𝑓*𝐵/d          - (2) <br>

#### a. Image preprocessing<br>
● Re-sizing stereo images to be of the same size (500, w). Here we used the cv2.resize() function to perform this action. This also helped in faster processing as originally the images were around (2000, 2000). Function ⇒ resizeImage().
#### b. Disparity Calculation <br>
● Once we have the stereo images of the same size, for each window patch in the left image, its correspondence location in the right image is retrieved by using normalized cross-correlation over the epipolar line. Functions ⇒ get_disparity_parallel(), compute_row() and norm_cross_correlation().<br>
● Then, the disparity is calculated using equation (1) for the correspondence locations. Here, the get_disparity_parallel() calls the compute_row() that fetches the window patches from the left image one by one and passes it to the norm_cross_correlation(). Then, the norm_cross_correlation() returns the output correlated map over the epipolar line back to compute_row() and compute_row() calculates the correspondence location. Once we have the correspondence location, the compute_row() calculates the disparity.
Functions ⇒ get_disparity_parallel(), compute_row() and norm_cross_correlation().
#### c. Depth Estimation <br>
● After having the disparity map, the depth of pixels is calculated using equation (2). Function ⇒ get_depth() <br>
● Here, some pixels would have the depth of infinity as their disparity was zero, and for visualization purposes, this infinity value is handled by the function replace_inf().

### Stereo Images: 
<img src = "https://user-images.githubusercontent.com/26826339/190955153-2f683d21-5f59-45ca-a751-5a31bbbf8530.png" width = "300" height = "300"/> <img src = "https://user-images.githubusercontent.com/26826339/190955172-830278e7-f22c-428c-9b20-58a082964034.png" width = "300" height = "300"/>
<br>
### Depth Map: 
<img src = "https://user-images.githubusercontent.com/26826339/190955068-4e75b18a-5adf-4f70-8492-a834bff36607.png" width = "400" height = "400"/>


### Semantics of calib.txt file terms => <hr>

cam0,1:        camera matrices for the rectified views, in the form [f 0 cx; 0 f cy; 0 0 1], where
  f:           focal length in pixels
  cx, cy:      principal point  (note that cx differs between view 0 and 1)

doffs:         x-difference of principal points, doffs = cx1 - cx0

baseline:      camera baseline in mm

width, height: image size

ndisp:         a conservative bound on the number of disparity levels;
               the stereo algorithm MAY utilize this bound and search from d = 0 .. ndisp-1

isint:         whether the GT disparites only have integer precision (true for the older datasets;
               in this case submitted floating-point disparities are rounded to ints before evaluating)

vmin, vmax:    a tight bound on minimum and maximum disparities, used for color visualization;
               the stereo algorithm MAY NOT utilize this information

dyavg, dymax:  average and maximum absolute y-disparities, providing an indication of
               the calibration error present in the imperfect datasets.
               
               
Resource => https://vision.middlebury.edu/stereo/data/scenes2014/
