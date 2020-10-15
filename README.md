# SFND 2D Feature Tracking

<img src="images/keypoints.png" width="820" height="248" />

The idea of the camera course is to build a collision detection system - that's the overall goal for the Final Project. As a preparation for this, you will now build the feature tracking part and test various detector / descriptor combinations to see which ones perform best. This mid-term project consists of four parts:

* First, you will focus on loading images, setting up data structures and putting everything into a ring buffer to optimize memory load. 
* Then, you will integrate several keypoint detectors such as HARRIS, FAST, BRISK and SIFT and compare them with regard to number of keypoints and speed. 
* In the next part, you will then focus on descriptor extraction and matching using brute force and also the FLANN approach we discussed in the previous lesson. 
* In the last part, once the code framework is complete, you will test the various algorithms in different combinations and compare them with regard to some performance measures. 

See the classroom instruction and code comments for more details on each of these parts. Once you are finished with this project, the keypoint matching part will be set up and you can proceed to the next lesson, where the focus is on integrating Lidar points and on object detection using deep-learning. 

## Dependencies for Running Locally
* cmake >= 2.8
  * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1 (Linux, Mac), 3.81 (Windows)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* OpenCV >= 4.1
  * This must be compiled from source using the `-D OPENCV_ENABLE_NONFREE=ON` cmake flag for testing the SIFT and SURF detectors.
  * The OpenCV 4.1.0 source code can be found [here](https://github.com/opencv/opencv/tree/4.1.0)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools](https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory in the top level directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./2D_feature_tracking`.

## Project Workflow Overview
1. Load the images into a ring buffer. 
2. Use OpenCV to apply a variety of keypoint detectors.
    - Shi-Tomasi
    - Harris
    - FAST
    - BRISK
    - ORB
    - AKAZE
    - SIFT
3. Use OpenCV to extract keypoint descriptors.
    - BRISK
    - BRIEF
    - ORB
    - FREAK
    - AKAZE
    - SIFT 
4. Use FLANN and kNN to improve on the brute force matching of keypoint descriptors.
5. Run these Algorithms in various combinations to compare performance.

## Covered Rubrics Points
### MP.1 Data buffer optimization
Checking the databuffer size and removing the element from the begaining of the databuffer if dataBuffer size is exceed max limit
```
if (dataBuffer.size() > dataBufferSize) 
{
	dataBuffer.erase(dataBuffer.begin());
}
dataBuffer.push_back(frame);
```

### MP.2 Keypoint detection
 - Code is implimented to cover MP.2 from the Lines 82-107 in MidTermProject_Camera_Student.cpp file

### MP.3 Keypoint removal
Code is implimented to cover MP.3 from the line 115-124 in MidTermProject_Camera_Student.cpp file

### MP.4 Keypoint descriptors
Code is implimented to cover MP.4 from the line 150-152 in MidTermProject_Camera_Student.cpp_

### MP.5 Descriptor matching
The function `matchDescriptors` in `matching2D_Student.cpp` contains a kind of decision tree structure, based on the settings of these string parameters:
- `descriptorCategory` either: `DES_BINARY` (binary), `DES_HOG` (histogram of gradients)
- `matcherType` either: `MAT_FLANN` (cv::FlannBasedMatcher), `MAT_BF` (brute force)
- `selectorType` either: `SEL_NN` (nearest neighbors), `SEL_KNN` (k nearest neighbors)

For the performance benchmarks (MP.7-9) below, `matcherType` was set to `MAT_BF` and `selectorType` was set to `SEL_KNN`, which implements match filtering based on the descriptor distance ratio.

### MP.6 Descriptor distance ratio
Code Is Implimented to cover MP.6 is available from Lines 66-72 in matching2D_Student.cpp
This distance ratio filter compares the distance (SSD) between two candidate matched keypoint descriptors. A threshold of `0.8` is applied and the minimum distance is selected as the correct match. This method eliminates many false-positive keypoint matches.

### MP.7 Performance evaluation 1
The number of keypoints within the bounding box of the preceding vehicle were counted for each detector type.
See the results in: MP7.csv in output folder

Harris had the fewest relevant keypoints, while the top three performers in this metric were:
1. FAST (consistently produced ~400 per image)
1. BRISK (254-297 keypoints per image)
1. AKAZE (155-179 keypoints per image)

### MP.8 Performance evaluation 2
The number of matched keypoints were then counted for each valid detector type and descriptor type combination, 35 in total. Note that SIFT descriptors could not be extracted with ORB detectors, and AKAZE descriptors worked only with AKAZE detectors.
See the results in: MP8_9.csv in output folder

The FAST detectors with BRIEF, SIFT, and ORB descriptors consistently produced the largest number of matched keypoints (~300 per image).

### MP.9 Performance evaluation 3

With FAST detectors, the three fastest descriptors were:
1. BRISK
1. BRIEF
1. ORB

All of these combinations consistently ran in less than `3 ms` total time. 

However, processing time must be balanced with the number of keypoints successfully matched in that time. For overall performance relevant to this project, the top three combinations were:
- FAST detectors and BRIEF descriptors
- FAST detectors and ORB descriptors
- FAST detectors and SIFT descriptors
