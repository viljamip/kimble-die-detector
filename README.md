# Kimble Die Detector 
*Problem:* I needed to build a computer vision system for a robot that plays the Kimble-board game. The plastic dome over the die distorts the pips and is prone to reflections. These issues make the detection a lot more difficult.

*Solution*: The detection is done in five major steps:

1: The image is captured with a webcam whose position is not constant with respect to the Kimble board. This is done inside the captureImage.py file.

2: A transformation matrix is calculated with homography. It matches similar points between the distorted camera image and a straight reference image and then calculates the needed transformation to straighten the camera image. All of this is done inside the findPerspective.py file. 

3: The die is always in the middle of the straightened image. The image from the previous step is cropped, made grayscale and noise is filtered with a bilateral filter. 

4: A gradient of the image is calculated with the cv2.laplacian() function. The gradient shows the rate of change of the image brightness across the image. This means that all sharp edges like the ones between the pips and the white body of the die show up. This image is then thresholded and cleaned up with erosion and dilation.

5: Small white circles that are likely to be the pips on the die are searched with cv2.findContours(). The image is then cropped around the middle of all such points. That cropped image is smoothed, and its contrast is adjusted with the CLAHE method. The pips are then recognized with a simple blob detector that filters by area and circularity. Lastly the minimum bounding box around all the detected circles is calculated with cv2.minAreaRect(). If the dimensions of that rectangle match typical values for the amount of pips detected the detection is likely accurate. If the box has dimensions that differ more than 10% there is likely a blob that was falsely detected. Each blob is removed one at a time and a bounding box is calculated for each combination of blobs until a bounding box of plausible dimensions is found. 

A video showing automated testing:
https://www.youtube.com/watch?v=402wiWS6kuw

## Installation and running
Numpy and OpenCV for python3 is required. I run my openCV installation inside a virtualenv.
You can make sure OpenCV is working by trying:
import cv2

cv2.\_\_version\_\_

Once OpenCV is working you can just run main.py with python3. You can try the automated testing with:
python3 main.py --test
It loads some test images from the testImages folder with glob.

Test images were captured with the testImageCapture.py. Hitting S on your keyboard saves the current frame to a file. Pressing Q quits the program.
