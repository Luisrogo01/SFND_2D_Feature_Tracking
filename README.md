# 2D Feature Tracking

## Details
The overall goal is to build the feature tracking part and test various detector / descriptor combinations to see which ones perform best for a sequential 10 image set. This project consists of four parts:

* First, setting up data structures and putting everything into a ring buffer to optimize memory load. 
* Then, integrating several keypoint detectors such as HARRIS, FAST, BRISK and SIFT and comparing them with regard to number of keypoints and speed. 
* In the next part, focusing on descriptor extraction and matching using brute force and also the FLANN approach. 
* In the last part, testing the various algorithms in different combinations and comparing them with regard to some performance measures. 

### Detecting Keypoints
In the literature (and in the OpenCV library), there is a large number of feature detectors , we can choose from. Depending on the type of keypoint that shall be detected and based on the properties of the images, the robustness of the respective detector with regard to both photometric and geometric transformations needs to be considered. In the next animation we can see how the FAST detector work with a sequential set of images.

<img src="https://media.giphy.com/media/jsO9ZpaJwUEY94ZZWZ/giphy.gif" width="400" height="150" />

### Descriptor extraction and matching
A descriptor is a vector of values, which describes the image patch around a keypoint. There are various techniques ranging from comparing raw pixel values to much more sophisticated approaches such as histograms of gradient orientation, helping us to assign similar keypoints in different images to each othes. There are Binary and Histograms of Oriented Gradients (HOG) based descriptors (In this project only SIFT uses HOG). We can see the result of applying a BRIEF descriptor with KNN selector type only on the preceding vehicle.

<img src="https://media.giphy.com/media/IhydHvnCflMvyL7wqE/giphy.gif" width="800" height="150" />

### Evaluating performance for the different combinations of dectectors and descriptors
#### Performance evaluation 1
Number of keypoints on the preceding vehicle for all 10 images.

|   |Img-0	|Img-1	|Img-2	|Img-3	|Img-4	|Img-5	|Img-6	|Img-7	|Img-8	|Img-9 |
|--- | --- | --- | --- |--- |--- |--- |--- |--- |--- |--- |
|SHITOMASI	|1370	|1301	|1361	|1358	|1333	|1284	|1322	|1366	|1389	|1339 |
|HARRIS	|115	|98	|113	|121	|160	|383	|85	|210	|171	|281 |
|FAST	|1824	|1832	|1810	|1817	|1793	|1796	|1788	|1695	|1749	|1770 |
|BRISK	|1339	|1304	|1327	|1291	|1256	|1208	|1237	|1232	|1196	|1215 |
|ORB	|600	|600	|600	|600	|600	|600	|600	|600	|600	|600 |
|AKZE	|1351	|1327	|1311	|1351	|1360	|1347	|1363	|1331	|1358	|1331 |
|SIFT	|1438	|1371	|1380	|1335	|1305	|1369	|1396	|1382	|1463	|1422 |

#### Performance evaluation 2
Mean number of matched keypoints for all 10 images using all possible combinations of detectors and descriptors.

| |BRISK	|BRIEF	|ORB	|FREAK	|AKAZE	|SIFT |
|--- | --- | --- | --- |--- |--- |--- |
|SHITOMASI	|85	|104	|100	|85	|N/A	|102 |
|HARRIS	|23	|31	|30	|25	|N/A	|28 |
|FAST	|37	|43	|42	|36	|N/A	|41 |
|BRISK	|33	|34	|32	|35	|N/A	|34 |
|ORB	|38	|28	|37	|26	|N/A	|49 |
|AKZE	|37	|40	|36	|36	|40	|39 |
|SIFT	|20	|24	|N/A	|20	|N/A	|36 |

#### Performance evaluation 2
Mean time (in ms) it takes for keypoint detection and descriptor extraction for the 10 images.

|  |BRISK	|BRIEF	|ORB	|FREAK	|AKAZE	|SIFT |
|--- | --- | --- | --- |--- |--- |--- |
|SHITOMASI	|15.8274	|15.0895	|17.9374	|43.561	|N/A	|30.9955 |
|HARRIS	|17.385	|16.2508	|18.5865	|42.8424	|N/A	|30.7213 |
|FAST	|2.19648	|2.06588	|4.61138	|32.5922	|N/A	|18.3495 |
|BRISK	|20.672	|20.0575	|30.5387	|51.1933 	|N/A	|36.5401 |
|ORB	|8.36329	|7.48412	|16.6431	|37.2672	|N/A	|29.8328 |
|AKAZE	|63.9534	|63.6301	|70.0348	|92.4194	|114.482	|80.3551 |
|SIFT	|91.9956	|94.4213	|N/A	|131.705	|N/A	|164.04 |

#### Top 3 detector / descriptor combinations
Evulating the number of matched keypoints and the time it takes to process detection and decription extraction we can mention the top 3 combinations:

1. FAST - BRIEF
2. FAST - BRISK
3. FAST - ORB

## Installation
### Dependencies for Running Locally
* cmake >= 2.8
  * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1 (Linux, Mac), 3.81 (Windows)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* OpenCV >= 4.1
  * This must be compiled from source using the `-D OPENCV_ENABLE_NONFREE=ON` cmake flag for testing the SIFT and SURF detectors.
  * The OpenCV 4.1.0 source code can be found [here](https://github.com/opencv/opencv/tree/4.1.0)
  * On Ubuntu use the following:
```
 [compiler] sudo apt-get install build-essential
[required] sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
[optional] sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev

cd ~/<my_working_directory>
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git

cd ~/opencv
mkdir build
cd build

cmake -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules -D CMAKE_BUILD_TYPE=Release -D WITH_GTK=ON -D OPENCV_ENABLE_NONFREE=ON ..
make -j8
sudo make install
sudo ldconfig
```
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools](https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
  
### Running
### Ubuntu

1. Clone this repo.
2. Make a build directory in the top level directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./2D_feature_tracking`.
