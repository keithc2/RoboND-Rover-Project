Writeup is down below (starts at line: 52)

[//]: # (Image References)
[image_0]: ./misc/rover_image.jpg
# Search and Sample Return Project
![alt text][image_0]

This project is modeled after the [NASA sample return challenge](https://www.nasa.gov/directorates/spacetech/centennial_challenges/sample_return_robot/index.html) and it will give you first hand experience with the three essential elements of robotics, which are perception, decision making and actuation.  You will carry out this project in a simulator environment built with the Unity game engine.  

## The Simulator
The first step is to download the simulator build that's appropriate for your operating system.  Here are the links for [Linux](https://s3-us-west-1.amazonaws.com/udacity-robotics/Rover+Unity+Sims/Linux_Roversim.zip), [Mac](	https://s3-us-west-1.amazonaws.com/udacity-robotics/Rover+Unity+Sims/Mac_Roversim.zip), or [Windows](https://s3-us-west-1.amazonaws.com/udacity-robotics/Rover+Unity+Sims/Windows_Roversim.zip).  

You can test out the simulator by opening it up and choosing "Training Mode".  Use the mouse or keyboard to navigate around the environment and see how it looks.

## Dependencies
You'll need Python 3 and Jupyter Notebooks installed to do this project.  The best way to get setup with these if you are not already is to use Anaconda following along with the [RoboND-Python-Starterkit](https://github.com/ryan-keenan/RoboND-Python-Starterkit).


Here is a great link for learning more about [Anaconda and Jupyter Notebooks](https://classroom.udacity.com/courses/ud1111)

## Recording Data
I've saved some test data for you in the folder called `test_dataset`.  In that folder you'll find a csv file with the output data for steering, throttle position etc. and the pathnames to the images recorded in each run.  I've also saved a few images in the folder called `calibration_images` to do some of the initial calibration steps with.  

The first step of this project is to record data on your own.  To do this, you should first create a new folder to store the image data in.  Then launch the simulator and choose "Training Mode" then hit "r".  Navigate to the directory you want to store data in, select it, and then drive around collecting data.  Hit "r" again to stop data collection.

## Data Analysis
Included in the IPython notebook called `Rover_Project_Test_Notebook.ipynb` are the functions from the lesson for performing the various steps of this project.  The notebook should function as is without need for modification at this point.  To see what's in the notebook and execute the code there, start the jupyter notebook server at the command line like this:

```sh
jupyter notebook
```

This command will bring up a browser window in the current directory where you can navigate to wherever `Rover_Project_Test_Notebook.ipynb` is and select it.  Run the cells in the notebook from top to bottom to see the various data analysis steps.  

The last two cells in the notebook are for running the analysis on a folder of test images to create a map of the simulator environment and write the output to a video.  These cells should run as-is and save a video called `test_mapping.mp4` to the `output` folder.  This should give you an idea of how to go about modifying the `process_image()` function to perform mapping on your data.  

## Navigating Autonomously
The file called `drive_rover.py` is what you will use to navigate the environment in autonomous mode.  This script calls functions from within `perception.py` and `decision.py`.  The functions defined in the IPython notebook are all included in`perception.py` and it's your job to fill in the function called `perception_step()` with the appropriate processing steps and update the rover map. `decision.py` includes another function called `decision_step()`, which includes an example of a conditional statement you could use to navigate autonomously.  Here you should implement other conditionals to make driving decisions based on the rover's state and the results of the `perception_step()` analysis.

`drive_rover.py` should work as is if you have all the required Python packages installed. Call it at the command line like this:

```sh
python drive_rover.py
```  

Then launch the simulator and choose "Autonomous Mode".  The rover should drive itself now!  It doesn't drive that well yet, but it's your job to make it better!  

**Note: running the simulator with different choices of resolution and graphics quality may produce different results!  Make a note of your simulator settings in your writeup when you submit the project.**



Writeup

0. Screen Resolution and Quality
  1152 x 720 with Good Quality

1. Description of perception_step()   ---rewrite with more details
  Q1: Which functions are used for what reason?
  Q2: What is the order of steps that you are taking?
  Q3: Where are the functions located in .py file?
  I initialized a size for destination and created a bottom_offset. I then created a copy of the image that the rover sees (Rover.img) for manipulation.
  I defined a source to get the transformation started based off of the image from the rover perspective. It defines, roughly four corners of a grid, not shown.
  As for the destination, I used the image.shape - where image is a copy of what the rover is currently seeing via the front camera - to give the coordinates of the desired location after the perspective transformation.
  The perspect_transform function used getPerspectiveTransform to create a matrix, M, then uses that to warp the perspective to a top-down view.
  For task number 3 in perception_step() I created a color threshold for three factors, the navigable terrain, the obstacles and the rock samples. I did this by calling the color_thresh function with two variables - this color thresh will be explained in greated detail later. This created a filter of colors so that the Rover could distinguish what it could, and couldn't, travel on as well as distinguish valuable rock samples for its grand mission.
  The next task desired for a current mapping of what the rover could see in that given instant, so I did as such by updating the vision_image of the Rover and displayed it using the color threshold created previously. In the instance of the terrain, I multiplied the outcome by 255 so that it was visible on the image displayed.
  The next step was to find out where the rover was in the world. This required two steps and the functions rover_coords() and pix_to_world(). First I used the color threshold and called the rover_coords() function to extract pixel coordinates with the rover being at the bottom center of the warped image (top-down) then used the Rover position (Rover_pos), the yaw value of the Rover (Rover.yaw), the world map and a scale factor to call the pix_to_world() function.
  The pix_to_world() function utilized the rotate_pix() and translate_pix() to convert where the Rover.pos was vs the world (x, y) and scaled the Rover.pos to match the world. Finally I updated the map, which was displayed at the bottom right corner of the simulation.
  To conclude the perception_step() I updated the Rover pixel distance and angles by using the to_polar_coords(). The distance was calculated simply by the Pythagorean theorem and the angles by the arctan(x, y) where (x,y) are the coordinates of the Rover via the world map after conversion.
  The order of the functions for perception.py is as follows: color_thresh(), rover_coords(), to_polar_coords(), rotate_pix(), translate_pix(), pix_to_world(), perspect_transform() and finally perception_step().

2. Description of decision_step()
  Q1: Which functions are used for what reason?
  Q2: What is the order of steps that you are taking?
  Q3: Where are the functions located in .py file?
  If the rover sees enough to make a decision (1), its in forward mode (2), if the options to move - via the angles - is above the threshold to decide to stop (3) and if the rover is not at the max speed, then accelerate without steering. If conditions 1, 2, and 3 are met but the rover is at its max velocity, the set the acceleration to 0 and let the rover coast. Also, if 1, 2, and 3 are met and the number of options to move - via distances - is above the threshold to stop, then steer the rover on average with limits of -15 degrees and 15 degrees. Finally, if for some reason the rover is accelerating but the rover velocity is still zero - simulating it is stuck on a rock - then stop accelerating, hit the brakes and turn -15 degrees.

3. color_thresh()
  For color_thresh() I removed the value of rgb_thresh because I wanted different values of that variable depending on if I were looking at terrain, obstacles or rock samples. I figured that rgb_thresh=(160, 160, 160) was enough to allow the rover to see the navigable terrain. Similarly rgb_thresh=(100, 100, 100) to distinguish obstacles. Finally, knowing that the rock samples it wanted to find where yellow, I allowed rgb_thresh=(200, 200, 0) to have a solid yellow color it was looking for.
