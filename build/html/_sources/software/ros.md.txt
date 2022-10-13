ROS2 사용 패키지 환경 설정
===========

ROS2 설치
-----
- ROS2 는 Galactic 버전을 사용한다.
- 아래 링크를 참조하여 설치를 진행한다.
- <https://docs.ros.org/en/galactic/Installation/Ubuntu-Install-Debians.html>

ROS2 패키지 환경 구성 및 의존패키지 설치 
------

- 미리 재공된 ROS2 패키지 폴더를  "~/robot/ros_ws/src/" 에 위치 시켜준다.
- rosdep을 사용하여 일부 의존성 패키지를 설치한다.
    ```bash
        $ mkdir -p ~/robot/ros_ws/src/
        $ cp -r ExaRobot_ROS2 ~/robot/ros_ws/src/

        #install rosdep
        $ sudo apt update
        $ sudo apt install -y python-rosdep
        $ sudo rosdep init 
        $ rosdep update

        $ cd ~/robot/ros_ws/
        
        #의존성 패키지 설치
        $ rosdep install --from-paths . --ignore-src -r -y

        # rosdep으로 설치되지 않은 의존성 패키지 설치
        $ sudo apt-get install ros-galactic-nav2-simple-commander 
        $ sudo apt-get install ros-galactic-cartographer-ros
        $ sudo apt-get install ros-galactic-spatio-temporal-voxel-layer
    ```

- 패키지 빌드를 수행한다.

```bash
    $ cd ~/robot/ros_ws
    $ colcon build
```

YDlidar SDK 설치
-----

```bash
    $ git clone https://github.com/YDLIDAR/YDLidar-SDK.git
    $ mkdir YDLidar-SDK/build 
    $ cd YDLidar-SDK/build
    $ cmake ..
    $ make
    $ sudo make install
```

realsense SDK 설치
-----

- 미리 제공된 ExaRobot_ROS2 의 패키지중 realsense-ros-node 는 librealsense_v2.50.0 의존성이 있어 정해진 버전을 사용한다.
```bash
    $ git clone https://github.com/IntelRealSense/librealsense -b v2.50.0
    $ mkdir librealsense/build 
    $ cd librealsense/build
    $ cmake ..
        ## python 모듈 빌드시
        $ cmake ../ -DBUILD_PYTHON_BINDINGS:bool=true

    $ make 
    $ sudo make install
    
    $ cd librealsense
    $ ./scripts/setup_udev_rules.sh
```