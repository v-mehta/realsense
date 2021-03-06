#Intel&reg; RealSense&trade; Technology - ROS Integration 

###(ROS Indigo + Ubuntu 14.04 [64-bit])
###Installation
#####Getting the camera to work on Linux

* Clone the source from the librealsense git repository https://github.com/IntelRealSense/librealsense.git and follow the "Installation Guide" for installing the library.
* Make sure that the software stack is installed properly and that the camera is working. This can be checked by connecting the camera to a USB3 port and running the "cpp-capture" sample program in the "librealsense/bin" folder.
If this does not work, you should first fix this issue before continuing with the ROS integration.
* Make sure "/usr/local/lib" is set in your "LD_LIBRARY_PATH".
* Copy the librealsense header files folder "librealsense/include/librealsense" to "/usr/local/include". 
 
	E.g. sudo cp -r \<librealsense_folder>/include/librealsense /usr/local/include


#####Building package:

* Setup ROS and create a local catkin workspace.
* Compile the realsense_camera package by executing the catkin_make command.

Successful execution of command will build target <b>“realsense_camera_nodelet”</b>

Sample launch files are available in camera/launch directory

<b>realsense_r200_rgbd.launch</b>

<b>realsense_r200_nodelet_standalone_preset.launch</b>

<b>realsense_r200_nodelet_standalone_manual.launch</b>

### Intel&reg; RealSense&trade; R200 Nodelet
Publishing stream data from the Intel® RealSense™ R200 (DS4) camera

#### Subscribed Topics
    None

#### Published Topics (default)

Color camera

    camera/color/image_raw (sensor_msgs/Image)
        Color rectified image. RGB format.
    camera/color/camera_info
        Calibration data

Depth camera

    camera/depth/image_raw (sensor_msgs/Image)
        uint16 depths in mm
    camera/depth/camera_info
        Calibration data
    camera/depth/points (sensor_msgs/PointCloud2)
        Registered XYZRGB point cloud.

Infrared1 camera

    camera/infrared1/image_raw (sensor_msgs/Image)
    camera/infrared1/camera_info
        Calibration data

Infrared2 camera

    camera/infrared2/image_raw (sensor_msgs/Image)
    camera/infrared2/camera_info
        Calibration data

#### Transform Frames
   The following command creates a pdf file that describes the transforms generated by the camera nodelet. You may refer to the [ROS tf wiki](http://wiki.ros.org/tf) for more commands.

    rosrun tf tf_monitor


#### Static Parameters

    Stream parameters:
        serial_no (string, default: blank)
            Specify the serial_no to uniquely connect to a camera, especially if multiple cameras are detected by the nodelet.
            This feature has been tested to work only on kernel version 4.4.0-040400-generic.
	    mode (string, default: preset)
	        Specify the mode to start camera streams. Mode comprises of height, width and fps. 
	        Preset mode enables default values whereas Manual mode enables the specified parameter values.
	    color_height (int, default: 480)
	        Specify the color camera height resolution.
	    color_width (int, default: 640)
	        Specify the color camera width resolution.
	    depth_height (int, default: 360)
	        Specify the depth camera height resolution.
	    depth_width (int, default: 480)
	        Specify the depth camera width resolution.
	    depth_fps (int, default: 60)
	        Specify the color camera FPS
	    depth_fps (int, default: 60)
	        Specify the depth camera FPS
	    enable_color (bool, default: true) 
	        Specify if to enable or not the color camera.
	    enable_pointcloud (bool, default: true) 
	        Specify if to enable or not the point cloud camera.
	    enable_tf (bool, default: true) 
	        Specify if to enable or not the transform frames.       
	    camera (string, default: "R200") 
	        Specify the camera name. 
    Camera parameters: 
    Following are the parameters that can be set only statically in the R200 camera:
        r200_depth_units : [1, 2147483647]
        r200_depth_clamp_min : [0, 65535]
        r200_depth_clamp_max : [0, 65535]
        r200_depth_control_estimate_median_decrement : [0 - 255]
        r200_depth_control_estimate_median_increment  : [0 - 255]
        r200_depth_control_median_threshold : [0 - 1023]
        r200_depth_control_score_minimum_threshold : [0 - 1023]
        r200_depth_control_score_maximum_threshold : [0 - 1023]
        r200_depth_control_texture_count_threshold : [0 - 31]
        r200_depth_control_texture_difference_threshold : [0 - 1023]
        r200_depth_control_second_peak_threshold : [0 - 1023]
        r200_depth_control_neighbor_threshold : [0 - 1023]
        r200_depth_control_lr_threshold : [0 - 2047]

####Services
    get_settings (camera/get_settings)
    
To get supported camera options with current value set. It returns string in options:value format where different options are seperated by semicolon.

####Dynamic Parameters

    Stream parameters:
        enable_depth (bool, default: true) 
          Specify if to enable or not the depth and infrared camera.
          Note: Infrared streams will be enabled or disabled along with depth stream.

    Camera parameters: 
    Following are the parameters that can be set dynamically as well as statically in the R200 camera.
        color_backlight_compensation
        color_brightness
        color_contrast
        color_gain
        color_gamma
        color_hue
        color_saturation
        color_sharpness
        color_enable_auto_white_balance
        color_white_balance            (Must be set only if color_enable_auto_white_balance is disabled)
        r200_lr_gain
        r200_emitter_enabled
        r200_lr_exposure               (Must be set only if r200_lr_auto_exposure_enabled is disabled)
    Following are the parameters that can only be set dynamically in the R200 camera.
        r200_lr_auto_exposure_enabled
        r200_auto_exposure_top_edge    (Must be set only if r200_lr_auto_exposure_enabled is enabled)
        r200_auto_exposure_bottom_edge (Must be set only if r200_lr_auto_exposure_enabled is enabled)
        r200_auto_exposure_left_edge   (Must be set only if r200_lr_auto_exposure_enabled is enabled)
        r200_auto_exposure_right_edge  (Must be set only if r200_lr_auto_exposure_enabled is enabled)

Note: For Autoexposure edge parameters, max value will go only upto the bounds of the infrared image.
E.g. For 320x240 infrared image, valid values are within 0-319 and 0-239)

Use rqt_reconfigure GUI to view and edit the parameters that are accessible via dynamic_reconfigure.
Command to launch GUI:

    $ rosrun rqt_reconfigure rqt_reconfigure

Command to change dynamic parameters using commandline:

    $ rosrun dynamic_reconfigure dynparam set /<node> <parameter_name> <value>
    E.g. $ rosrun dynamic_reconfigure dynparam set /RealsenseNodelet color_backlight_compensation 2


###Running the R200 nodelet

Use the following command to launch the camera nodelet. You will notice the camera light up.

    $ roslaunch realsense_camera realsense_r200_nodelet_standalone_preset.launch

View using RVIZ:

For color, infrared1 and infrared2 streams, you can open <b>RVIZ</b> and load the published topics.

You can also open RVIZ and load the provided RVIZ configuration file: realsenseRvizConfiguration1.rviz.
```
   $ roscd realsense_camera
   $ rosrun rviz rviz -d rviz/realsenseRvizConfiguration1.rviz
```
For the point cloud stream, before loading its corresponding topic, set the camera_depth_optical_frame using following command:

    $ rosrun tf static_transform_publisher 0.0 0.0 0.0 0.0 0.0 0.0 map camera_depth_optical_frame 100


View using other commands:
For color stream

    $ rosrun image_view image_view image:=/camera/color/image_raw

For depth stream

    $ rostopic echo /camera/depth/image_raw

    $ rostopic echo /camera/depth/camera_info

For pointcloud

    $ rosrun pcl_ros convert_pointcloud_to_image input:=/camera/depth/points output:=/my_image

    $ rosrun image_view image_view image:=/my_image

For viewing supported camera settings with current values:

    $ rosservice call /camera/get_settings

<b>Tech and dependencies</b>
* librealsense.so

<b>System:</b>
* Linux 14.04+
* ROS Indigo
* R200 (DS4) camera

** The ROS integration has been tested on a 64bit machine with Linux 14.04 (Trusty) and ROS Indigo.

###Unit Tests
The Unit Tests can be executed using either of the methods:

Using rostest command with test files

    $ rostest realsense_camera <test_filename>
    E.g. rostest realsense_camera realsense_r200_depth_only.test 

Using rosrun command

    $ roslaunch realsense_camera realsense_r200_nodelet_standalone_manual.launch

    $ rosrun realsense_camera realsense_camera_test <args>
    E.g. rosrun realsense_camera realsense_camera_test enable_depth 1 depth_encoding 16UC1 depth_height 360 depth_width 480 depth_step 960 enable_color 1 color_encoding rgb8 color_height 480 color_width 640 color_step 1920

Sample test files are available in test directory

<b>realsense_r200_rgbd.test</b>

<b>realsense_r200_color_only.test</b>

<b>realsense_r200_depth_only.test</b>

<b>realsense_r200_resolution.test</b>


Both of these methods first starts "RealsenseNodelet" for Intel® RealSense™ R200 (DS4) camera and then executes all the unit tests.

###Developer API:
Refer to the function definitions in [realsense_camera_nodelet.h](src/realsense_camera_nodelet.h)


###Limitations:
* Currently, the camera nodelet has been tested to work only for R200 cameras.

* Currently, the camera nodelet only supports the following formats:
    * Color stream:    RGB8
    * Depth stream:    Y16
    * Infrared stream: Y8

* The camera does not provide hardware based depth registration/projector data. 
Hence the launch file "realsense_r200_rgbd.launch" will not generate data for the following topics:  
    * /camera/depth_registered/hw_registered/image_rect_raw  
    * /camera/depth_registered/points  
    * /camera/depth_registered/hw_registered/image_rect  
    * /camera/depth_registered/image  
    * /camera/depth/disparity  
    * /camera/depth_registered/disparity  

* If there are multiple R200 cameras connected to a system, the nodelet can be launched for a particular camera 
by specifing the serial_no parameter in the launch file. But it has not been tested to launch nodelets simultaneouly for multiple cameras.

