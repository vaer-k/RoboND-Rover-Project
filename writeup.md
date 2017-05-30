# Project: Search and Sample Return
## An autonomous rover exercise in decision tree navigation, image transformation and image analysis

---


### Goals of project

**Training / Calibration**  

* Record training images using the given Unity-based rover simulator
* Detect and mark navigable terrain, obstacles and samples of interest (golden rocks)
* Perform appropriate image processing steps including perspective transfor and color thresholding to build a map or terrain from simulated raw camera images.

**Autonomous Navigation / Mapping**

* Use the image processing steps described above to create a map and provide rover with terrain information.
* Build a tree of conditional statements that use the image analysis to decide how to issue throttle, brake and steering commands.
* Address [course rubric requirements](https://review.udacity.com/#!/rubrics/916/view) and achieve autonomous navigation capable of mapping at least 40% of terrain with at least 60% fidelity. Additionally, at least one rock sample is required to be discovered.

[//]: # (Image References)

[image1]: ./misc/camera_feed.png
[image2]: ./misc/warped.png
[image3]: ./misc/color_thresh1.png
[image4]: ./misc/color_thresh2.png 

---
### Project analysis

#### Image processing

The [course rubric](https://review.udacity.com/#!/rubrics/916/view) required that I achieve autonomous navigation capable of mapping at least 40% of terrain with at least 60% fidelity. Additionally, at least one rock sample was required to be discovered.

Mapping the terrain involved writing perception steps performing image analysis of simulated rover camera feeds. Using the OpenCV computer vision library, I performed translation, rotation and scaling of simulated raw camera images to build a world map, or top-down view, of the surrounding terrain environment. Next, I performed color-thresholding of image pixels to identify important features, including navigable terrain, obstacles and rock samples. 

Camera feed:

![alt text][image1]

Top-down world map with colors marking navigable terrain and obstacles:

![alt text][image2]

The above processes performed reasonably well but were unable to reliablty meet the requirements of the rubric. Although they reliably identified rock samples, the percentage of terrain mapped and the fidelity of the mappings fell short of expectations. I began looking for altenative solutions or tweaks to the processing that might help me improve the rover's mapping fidelity, which was the rubric metric that suffered the most. After noticing that the warped images from the camera feed to top-down perspective appeared most accurate in the regions closest the rover's camera point of view, I decided to consider only image data at the bottom of images, i.e. closest to camera, when marking terrain on the map as an obstacle or navigable surface. Thus I was able to improve fidelity by approximately twenty percent.

Before:

![alt test][image3]

After:

![alt test][image4]

#### Navigation

To improve the percentage of terrain mapped and the number of samples found, I needed to ensure that my rover traversed as much of the terrain as possible. To do this, the rover would need to avoid obstacles, extricate itself when stuck or cornered, and to pass as near to as many rock samples as possible. This was achievable by following the right wall of corridors in the terrain environment. 

To navigate the terrain, the rover was designed to follow the mean steering angle of the direction of the most navigable terrain. This was enough to succeed in mapping large portions of the map, but the rover frequently ran into obstacles and travelled to far from rock samples to allow for detection. To remedy this, I weighted the angles of navigable terrain by nearness and direction, so that closer, more rightward angles were preferred. 

This caused the rover to hug the right wall of the map as it traversed the terrain, however it did not allow the rover to extricate itself when stuck on an obstacle. To fix this, I programmed the rover make a copy of its position on the map every five seconds, then every five seconds during throttle, it checked to see if its position had changed. If it had not, it stopped, released brakes, turned 15 degrees and proceeded as usual. 

These modifications were enough to bypass obstacle collisions, infrequent as they were. Additionally, because all rock samples were found close to the walls, by following the right wall the rover was guaranteed to pass rock samples. Thus the rover was able to achieve a mapping percentage that far exceeded the rubric requirements and that picked up multiple rock samples.

#### Improvement

There are many ways this project could be improved. First, rather than a explicitly programmed decision tree for navigation, it might be suitable to train a decision tree using some labelled data and gradient descent. Additionally, I could probably have done better at choosing the pixel color ranges used for identifying objects in vision. 

#### Settings

All simulations were run with 1600x900 screen resolution in windowed mode at 50 FPS and with "fantastic" graphics quality.
