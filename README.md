# 6DOF pose estimation with Aruco marker and ROS


ArUco is a simple yet great library for augmented reality applications. In this tutorial, I’m gonna show you how to track ArUco marker and estimate their 6DOF pose with ROS.
For this tutorial, you only need a USB camera. You need to calibrate your camera before first. If you don’ know how to that just follow my other tutorial on
[camera calibration with ROS.](http://ros-developer.com/2017/04/23/camera-calibration-with-ros/ "camera calibration with ROS")




## Installing the required package:
So first let’s install the required packages:

```
sudo apt-get install ros-kinetic-usb-cam ros-kinetic-aruco-ros
```

## Launch Files

You need two launch files, 

usb_cam_stream_publisher.launch

```
<launch>
<arg name="video_device" default="/dev/video0" />
<arg name="image_width" default="640" />
<arg name="image_height" default="480" />

<node name="usb_cam" pkg="usb_cam" type="usb_cam_node" output="screen" >
<param name="video_device" value="$(arg video_device)" />
<param name="image_width" value="$(arg image_width)" />
<param name="image_height" value="$(arg image_height)"/>
<param name="pixel_format" value="mjpeg" />
<param name="camera_frame_id" value="usb_cam" />
<param name="io_method" value="mmap"/>
</node>
</launch>
```

The other launch file  aruco_marker_finder.launch:

```
<launch>

<arg name="markerId" default="701"/>
<arg name="markerSize" default="0.05"/> <!-- in meter -->
<arg name="eye" default="left"/>
<arg name="marker_frame" default="marker_frame"/>
<arg name="ref_frame" default=""/> <!-- leave empty and the pose will be published wrt param parent_name -->
<arg name="corner_refinement" default="LINES" /> <!-- NONE, HARRIS, LINES, SUBPIX -->


<node pkg="aruco_ros" type="single" name="aruco_single">
<remap from="/camera_info" to="/usb_cam/camera_info" />
<remap from="/image" to="/usb_cam/image_raw" />
<param name="image_is_rectified" value="True"/>
<param name="marker_size" value="$(arg markerSize)"/>
<param name="marker_id" value="$(arg markerId)"/>
<param name="reference_frame" value="$(arg ref_frame)"/> <!-- frame in which the marker pose will be refered -->
<param name="camera_frame" value="base_link"/>
<param name="marker_frame" value="$(arg marker_frame)" />
<param name="corner_refinement" value="$(arg corner_refinement)" />
</node>

</launch>

```
## Publishing Images From Camera Data and Estimating the Pose

publishing images from your camera:

```
roslaunch usb_cam_stream_publisher.launch
```


finding the markers
```
roslaunch aruco_marker_finder.launch markerId:=701 markerSize:=0.05
```

To see the results, open rqt_gui:
```
rosrun rqt_gui rqt_gui
```

The topic name of the pose of markers is   /aruco_single/pose. You can monitor it by
```
rostopic echo /aruco_single/pose
```


[![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/sV7vOTvUCx8/0.jpg)](https://www.youtube.com/watch?v=sV7vOTvUCx8)
![alt text](https://img.shields.io/badge/license-BSD-blue.svg)
