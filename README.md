# darknet-ros-fp16

darknet_ros + ROS2 Foxy + OpenCV4 + CUDA 11.2 + __CUDNN (FP16)__ :fire::fire::fire:

- [English (GitHub Wiki)](https://github.com/Ar-Ray-code/darknet_ros_fp16/wiki/Darknet_ros_FP16-Report-(1.3x-faster)-%F0%9F%94%A5)
- [Japanese (zenn)](https://zenn.dev/array/articles/4c82fc8382e62d)

## Main changes

- __Support for YOLO v4__ : Switched the submodule to the master branch of [AlexeyAB/darknet.](https://github.com/AlexeyAB/darknet)
- __Removed IPL__ : Switched from IPL to CV::Mat for OpenCV4 support.
- __Support cuDNN + FP16__

## Try on Docker :whale:

[DockerHub](https://hub.docker.com/r/ray255ar/darknet-ros-fp16)

### Requirements

- Ubuntu Desktop + NVIDIA Graphics Driver
- v4l2-camera (Connect to `/dev/video*`)
- NVIDIA Graphics Card (Volta , Turing , Ampere) 
- Docker + [NVIDIA-Docker](https://github.com/NVIDIA/nvidia-docker) 
  - This docker image is using `cuda:11.4.2` .

- xhost (To install xhost , run `$ sudo apt install xorg` .)

### Installation & Run

```bash
xhost +
# Pull docker image from dockerhub
docker pull ray255ar/darknet-ros-fp16
# Run 
docker run --rm -it \
	--device /dev/video0:/dev/video0:mwr \
	-e DISPLAY=$DISPLAY --runtime nvidia \
	-v /tmp/.X11-unix:/tmp/.X11-unix ray255ar/darknet-ros-fp16 \
    /bin/bash yolov4-tiny-docker.bash
```



## Installation :turtle:

### Requirements
- ROS2 Foxy
- OpenCV 4.2
- CUDA 10 or 11 (tested with CUDA 11.5.119)
- cuDNN 8.3 ([Installation tutorial](https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html))

### Installation
```bash
$ sudo apt install ros-foxy-desktop ros-foxy-v4l2-camera
$ source /opt/ros/foxy/setup.bash
$ mkdir -p ~/ros2_ws/src
$ cd ~/ros2_ws/src
$ git clone --recursive https://github.com/kaancolak/darknet_ros_yolov4.git
$ ./darknet_ros_fp16/darknet_ros/rm_darknet_CMakeLists.sh
$ cd ~/ros2_ws
$ colcon build --symlink-install
```
### Edit CMakeLists.txt

#### Options

When each option is turned off, the respective compile option will be disabled. This item is for benchmarking purposes, as it will be automatically disabled if the required libraries are not installed.

```cmake
set(CUDA_ENABLE ON)
set(CUDNN_ENABLE ON)
set(FP16_ENABLE ON)
```

#### cuDNN (FP16)

Darknet can be made even faster by enabling CUDNN_HALF(FP16), but you need to specify the appropriate architecture. 

FP16 is automatically enabled for GPUs of the Turing or Ampere architecture if the appropriate cuDNN is installed. To disable it, change line 12 to `set(FP16_ENABLE OFF)`.

The Jetson AGX Xavier and TITAN V support FP16, but due to their Volta architecture, auto-detection is not possible. (Sorry... :( )

In that case, please comment out line 17 `set(CMAKE_CUDA_ARCHITECTURES 72)`

#### Download darknet weights

Since the weights to be downloaded are large, you can select the weights to be downloaded by the options.

- DOWNLOAD_YOLOV2_TINY (Default : `ON`)
- DOWNLOAD_YOLOV3 (Default : `OFF`)
- DOWNLOAD_YOLOV4 (Default : `ON`)
- DOWNLOAD_YOLOV4_CSP (Default : `OFF`)
- DOWNLOAD_YOLOV4_TINY (Default : `ON`)
- DOWNLOAD_YOLOV4_MISH (Default : `OFF`)

```cmake
set(DOWNLOAD_YOLOV2_TINY ON)
set(DOWNLOAD_YOLOV3 OFF)
set(DOWNLOAD_YOLOV4 ON)
set(DOWNLOAD_YOLOV4_CSP OFF)
set(DOWNLOAD_YOLOV4_TINY OFF)
set(DOWNLOAD_YOLOV4_MISH OFF)
```



### Demo

Connect your webcam to your PC.

```bash
$ source /opt/ros/foxy/setup.bash
$ source ~/ros2_ws/install/local_setup.bash
$ ros2 launch darknet_ros darknet_ros.launch.py
```

![example](https://user-images.githubusercontent.com/67567093/117596596-a2c8db00-b17e-11eb-90f9-146212e64567.png)



## Performance

Using YOLO v4 consumes a lot of GPU memory and lowers the frame rate, so you need to pay attention to your PC specs.

### Test Machine

| Topics | Spec                                    |
| ------ | --------------------------------------- |
| CPU    | Intel Core i9 12900KF                   |
| RAM    | 64GB DDR4                               |
| GPU    | NVIDIA GeForce RTX 2080 Ti (GDDR6 11GB) |
| Driver | 495.29.05                               |

### Performance (using cuDNN FP16)

YOLO v4 : 48fps

Scaled YOLO v4 : 80fps

YOLO v4-tiny : 215fps

YOLO v4x-mish : 32fps

YOLO v2-tiny : 205fps (Min : 24fps)

> Note : YOLOv2-tiny is deprecated.

![E2tRQvnUcAQqn8O](https://user-images.githubusercontent.com/67567093/121984014-35d3e100-cdcd-11eb-9959-b1063a9a0b2b.jpeg)



## Acknowledgment
 I am not a good programmer, but I was able to implement it with the help of many repositories. Thank you to [AlexeyAB](https://github.com/AlexeyAB)'s [darknet](https://github.com/AlexeyAB/darknet) , [legged robotics](https://github.com/leggedrobotics)'s [darknet_ros](https://github.com/leggedrobotics/darknet_ros), and [Tossy0423](https://github.com/Tossy0423/)'s [darknet_ros](https://github.com/Tossy0423/yolov4-for-darknet_ros/) !
