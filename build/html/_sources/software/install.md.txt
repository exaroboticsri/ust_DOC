Python S/W 환경 설정
===========

의존성 패키지 설치
-----

```bash
    pip3 install PyQt5
    pip3 install numpy
    pip3 install pyserial
    pip3 install pyttsx3
    pip3 install opencv-python
    pip3 install opencv-contrib-python
    pip3 install psutil
    pip3 install SpeechRecognition
        # 의존성: sudo apt-get install flac
    pip3 install pyaudio
    # 오류 발생시:  sudo apt-get install portaudio19-dev -y
```

환경 구성 및 실행
-----

- 이용하는 장치의 udev rule 를 지정한다.

```bash
    # robot serial
    echo  'KERNEL=="ttyUSB*", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6015", MODE:="0666", GROUP:="dialout",  SYMLINK+="ttyRobot"' >/etc/udev/rules.d/robot_exa.rules
    
    # lidar serial
    echo  'KERNEL=="ttyUSB*", ATTRS{idVendor}=="10c4", ATTRS{idProduct}=="ea60", MODE:="0666", GROUP:="dialout",  SYMLINK+="ttyG6"' >/etc/udev/rules.d/ydlidar_g6.rules

    service udev reload
    sleep 2
    service udev restart
```

- 미리 제공된 ExaRobotCtrl 파일을 복사하고 실행시킨다.
  - 먼저 ROS 환경이 구성되어야한다.

    ```bash
        $ mkdir ~/robot
        $ cp -r ExaRobotCtrl ~/robot

        $ cd ~/robot/ExaRobotCtrl/core.sh

    ```


Jetson Inference 설치(Jetson 전용)
------

- 미리 제공된 앱보드 용 ExaRobotUI 의존성 패키지이다.
- Jetson 기반 플렛폼에서 GPU를 사용한 물체감지, 얼굴감지 등 AI 기술사용시 필요하다. 

<https://github.com/dusty-nv/jetson-inference>

```bash
$ sudo apt-get update
$ sudo apt-get install git cmake libpython3-dev python3-numpy
$ git clone --recursive https://github.com/dusty-nv/jetson-inference
$ cd jetson-inference
$ mkdir build
$ cd build
$ cmake ../

#GUI-Select download model
#GUI-Select pytortch install 

$ make -j$(nproc)
$ sudo make install
$ sudo ldconfig
```
