---
layout: post
title: Mac에서 Docker기반 ROS2 Gazebo 환경구축
---

### ROS(Robot Operation System)

ROS는 로봇을 쉽게 개발하고 제어할 수 있는 종합적 환경을 제공하는 운영체제이다. 운영체제이지만, 일반적인 윈도우, 맥, Ubuntu와 다르게 그 위에서 작동하는 Middleware와 같은 플랫폼이다. 다양한 하드웨어 업체에서 ROS에서 사용할 수 있는 드라이버와 패키지를 제공하고 있으며, PC의 제약 없이 사용할 수 있다는 장점이 존재한다.

**ROS는 Ubuntu, Mac, Windows 상관 없이 설치가 가능하다.** 하지만, ROS에서 사용하는 모든 패키지들이 환경에 상관없이 사용 가능하다는 것은 아니다. 한 가지의 예로 살펴보자. 딥러닝을 할 때 Python을 주로 사용하고, Python에서 OpenCV를 패키지로 불러와 사용한다. OpenCV를 pip 명령어로 설치하게 된다면, CPU를 사용해 동작한다. 이렇게 설정한 환경은 **CUDA를 사용해 빌드한 OpenCV에 비해 성능이 처참하게 떨어진다.** 여기서 문제점을 찾아볼 수 있다. **환경에 상관 없이 ROS를 설치해서 사용할 수 있으나, CUDA 기반의 OpenCV는 NVIDIA GPU를 사용한 플랫폼에서만 사용할 수 있다.**

참고로 ROS에서는 OpenCV를 다음과 같은 방식으로 사용하고 있다. PC에 설치된 OpenCV를 ros-cv-bridge라는 매니저가 불러와서 사용할 수 있도록 연결해주고 있다.

본 글에서도 이러한 문제를 해결하기 위해 노력하지만, **아직 적절한 해결 방법을 찾지 못했다.** 따라서, 맥에서 본 환경을 세팅할 때에는 다음과 같은 문제들이 존재한다.

> 1. OpenCV와 같은 플랫폼을 사용할 수 없다. 이는 Mac에는 CUDA가 없기 때문이다. 하지만, Mac의 NPU와 GPU를 이용해 OpenCV를 구성할 수 있는데, 이것을 Mapping 하는 형태로 환경 세팅을 추후에 시도해 볼 것이다.
> 2. OpenGL을 사용할 수 없다. M1 Mac은 OpenGL을 사용하지 않는다. ROS의 시뮬레이션 환경인 Gazebo는 OpenGL을 사용한다. 이를 구동할 수가 없다.

이러한 문제를 제쳐두고서라도, 가벼운 Node의 개발은 Mac에서도 가능하기에 이 환경을 구성해보려 한다.

### Docker

Docker는 OS의 가상화 플랫폼이다. Hypervisor는 Type1과 Type2로 구분된다.

**Type1**은 하드웨어 위에 Hypervisor를 구축하고, 여러 OS를 위에 구현하는 개념이다. 각각의 독립된 환경을 제공하기에는 어렵지만, 하드웨어의 자원 낭비를 줄일 수 있는 이점이 존재한다.
**Type2**는 OS 위에 Hypervisor를 구축하고, OS를 위에 추가로 구현하는 방식이다. VMWare, Virtual Box, UTM 등 일반적으로 사용하는 가상화 SW가 여기에 해당한다. 많은 자원을 필요로 하나, 다른 장치에 이식이 쉽고, 높은 격리성과 캡슐화가 장점으로 작용한다.

![img](https://velog.velcdn.com/images/addps5012/post/60bedcbf-4b7d-4a59-b554-71a9bf9451ce/image.png)

**Docker는 Type1과 Type2 그 사이에 해당한다.** Virtual Machine과 다르게 운영체제 위에 Hypervisor가 붙지 않고, 도커 엔진 위에 여러 컨테이너가 바로 구동된다. App마다 다른 OS가 하위에 설치되야 하는 Virtual Machine기법과 다르게 자원 낭비가 심하지 않고, **Container**를 통해 다른 쉽게 이식할 수 있는 환경을 제공한다.

![img](https://velog.velcdn.com/images/addps5012/post/dec7881a-40cc-4e21-aaf6-f7d8ed662caf/image.png)

내가 만든 도커의 컨테이너를 **Image**로 만들고, Image를 파일이나 **Docker Hub**를 통해 쉽게 다른 사람에게 전달해 줄 수 있는 장점이 있다.

**Docker는 Layer 기법을 사용한다**는 특징이 있다. 기존의 만들어둔 환경에 추가적인 기능이 필요하다면, 그 이미지를 활용해 이전 단계를 생략할 수 있다. DockerHub와 Layer 기법은 엄청난 상호 작용을 하는데, 다른 사람이 내가 필요한 환경까지 구축해 이미지를 Docker Hub에 올려뒀다면, 이를 가져와 간단한 작업만 해주면된다.
![img](https://velog.velcdn.com/images/addps5012/post/1612b146-864b-41f5-bfcc-1e077025510e/image.png)

## 🖥️ ROS 개발환경 구성하기

### Docker 설치하기

Docker는 기본적으로 CLI(Command Line Interface)를 지원한다. Homebrew를 이용해 Docker를 설치해도 되나, [도커 공식 홈페이지](https://www.docker.com/)에서 받아서 그냥 설치하면 된다.

> - **Ubuntu 환경에서는 APT를 사용해 CLI 환경으로 구성할 수 있다.**
> - **Windows 환경에서는 WSL(Windows subsystem for Linux)를 설치한 뒤, Docker를 설치하면 된다. 하지만, 이 때문에 자원의 효율성이 떨어진다.**

Docker를 설치했다면, 다음 명령어를 통해 이미지를 관리할 수 있다. 이미지는 Docker file을 통해 레이어들을 직접 커스텀해 생성하는 방식이 있고, 단순히 Pull을 통해 이미지를 Docker Hub에서 불러오는 방식이 존재한다.

```null
# 도커 프로세스 목록 보기
docker ps

# 이미지 목록 보기
docker images

# Docker hub에서 이미지 가져오기
docker pull [이미지 이름]:[태그]

# Docker file을 통한 이미지 생성
docker build -t [이미지 이름]:[태그] [Dockerfile Path]

# 현재 컨테이너를 이미지로 저장하기
docker commit [컨테이너 ID] [이미지 이름]:[태그]
```

Docker Hub에 ROS로 검색을 하게 된다면, [ROS의 공식 도커 이미지](https://hub.docker.com/_/ros)가 제공된다. 태그에서 원하는 버전을 검색해보면, 다양한 버전이 제공되고 있음을 알 수 있다.
![img](https://velog.velcdn.com/images/addps5012/post/5568ee49-887d-4c10-8e97-0997666768a4/image.png)

용량이 가장 클수록 가장 많은 패키지들이 포함되어 있다는 뜻이다. 용도에 맞게 받으면 되나, 개발을 공부할 때에는 가장 많은 환경을 모두 구성해야 한다. 이미지 태그를 눌러보면, 어떤 레이어로 구성되어 있는지 볼 수 있다.
![img](https://velog.velcdn.com/images/addps5012/post/1c3620df-1a99-49bb-b9d4-1d81d8bfb6db/image.png)

### Docker Image 가져오기

다음 명령어를 통해 Docker Hub에서 이미지를 불러온다. 여기서는 **ros:melodic-perception-bionic**를 사용해 보자.

```null
# 이미지 불러오기
docker pull ros:melodic-perception-bionic

# 이미지 확인하기
docker images

# 이미지 실행하기(이미지 -> 컨테이너)
# 여기서 it은 실행과 동시에 컨테이너 내부에 접속함을 의미
docker run -it ros:melodic-perception-bionic

# 실행중인 컨테이너 확인하기
docker ps

# 컨테이너 종료하기
docker stop [컨테이너 ID]

# 컨테이너에서 나가기(컨테이너 내부에서)
exit
```

이 상태에서 우리는 ROS Node를 동작시키고 실행할 수 있다. Docker를 사용할 때, 유의할 점은 **현재 환경이 저장되지 않는다는 점이다. 현재의 환경을 저장하고 싶으면 commit을 통해 새로운 image로 생성해줘야 지속적으로 사용이 가능하다.** 여기서 환경 설정할 것은 다음과 같다.

> - **Docker 이미지에 모든 ROS 패키지들이 포함된 환경을 구성하기**
> - **쉽게 실행할 수 있는 Shell Script 작성하기**

### 필요한 환경으로 Docker Image 구성하기

먼저 받은 Docker Image에 나머지 ROS Package들을 설치해서 ROS의 모든 기능을 사용할 수 있는 환경으로 설정한다. melodic-perception-bionic으로 받은 이미지이므로 아래 명령어를 실행하면, 설치되지 않은 남은 패키지들만 설치된다.

```null
# 도커 이미지 구동
docker run -it ros:melodic-perception-bionic

## 컨테이너 내부에서 ROS Package 설치
# APT 패키지 목록 갱신
apt update

# ROS Desktop 설치하기
apt install ros-melodic-desktop-full

# 필요한 기타 Tool 설치하기(사용자마다 다르게!)
apt install net-tools nano vim tree
```

이제 이미지 내부의 사용자 정보를 설정해보자. 먼저, 맥에서 터미널을 하나 더 띄어서 **MAC의 사용자 ID(UID)를 확인**한 뒤, 동일하게 사용자를 생성해야 Volume Mapping을 했을 때, 정상적으로 접근할 수 있다.

```null
# MAC 터미널에서 사용자 UID 확인
echo $UID
```

나의 경우 501로 확인된다. 대부분의 맥 사용자는 501이며, 우분투는 1000이 기본 사용자 ID로 되어 있을 것이다. 이 이미지에서도 사용자를 생성하면 1000으로 기본 ID가 지정된다. 아래 명령어를 컨테이너 내부 쉘에서 실행하면 된다.

```null
# 사용자 추가하기
# adduser [사용자 이름]
adduser addps5012

# UID 변경하기
usermod -u 501 addps5012

# sudo 권한 부여하기(Editor는 편한걸로 사용!)
nano /etc/sudoers

# 아래 구문을 추가한 뒤, 저장한다.
# [사용자 이름]	ALL=(ALL:ALL)	ALL
addps5012	ALL=(ALL:ALL)	ALL
```

이제 기본적인 이미지 설정은 모두 마쳤으니, 이를 Image로 저장해둬야 한다. 이 작업은 맥의 터미널에서 실행한다. 실행중인 컨테이너의 ID를 확인해서 이를 이미지로 Commit 하면 된다.
![img](https://velog.velcdn.com/images/addps5012/post/7f7e5e3a-c8f6-427e-9aeb-ee38fbccc60e/image.png)

```null
# 컨테이너 ID 확인
docker ps

# 컨테이너를 Image로 Commit
# docker commit [컨테이너 ID] [이미지 이름]:[태그]
docker commit 470ac159e037 addps5012/ros:melodic_osx
```

이제 컨테이너를 나가면 모든 사전 환경 설정이 완료된다.

```null
## 컨테이너 내부 쉘의 경우
exit

## 컨테이너 외부 쉘의 경우
# 컨테이너 ID 확인
docker ps

# 컨테이너 종료
# docker stop [컨테이너 ID]
docker stop 470ac159e037
```

### XQuartz 설치하기

우리는 앞선 작업에서 Image 설정 작업을 완료했다. 그런데, **"컨테이너 내부에서 Rviz나, rqt_graph를 실행하게 되면, 우리가 볼 수 있을까? CLI 환경인데.."**

Linux에서는 **X11 Forwarding**이라는 것이 존재한다. SSH의 연결을 통해, GUI로 구성되는 디스플레이 출력을 전달해주는 기능이다. Ubuntu에서는 X11 Client, Mac에서는 XQuartz, 윈도우에는 XWindows를 사용하면 이 기능을 사용할 수 있다.

우선, [XQuartz 공식 사이트](https://www.xquartz.org/)에서 XQuartz를 받아 설치한다. XQuartz를 실행하면, 아무것도 뜨지 않을 수 있는데, 맥 좌측 상단에는 모든 메뉴가 표시된다.
![img](https://velog.velcdn.com/images/addps5012/post/f0ca44c6-6d9b-4ce5-b4f4-727a26d1b07d/image.png)

여기서 설정으로 들어간 뒤, 보안 탭에서 **네트워크 클라이언트에서의 연결을 허용** 항목을 체크해준다.
![img](https://velog.velcdn.com/images/addps5012/post/15ce25c8-4c90-451b-a0ca-eee4cff800bc/image.png)

### 도커 이미지 쉽게 실행하기

도커 이미지를 실행하는 명령어는 사실 단순하지 않다. 단순히 start, stop, run 명령어를 사용하지만, 여러 옵션을 부여할 수 있다. 매번 실행할 때마다 만들기 쉽지 않기에 나는 shell script 파일을 만든 뒤, 거기에 저장하고 이를 실행한다. 내가 사용하는 run_xycar.sh의 파일이다.

```null
PS_NAME=ros_xycar
xhost +
xhost + ${homename}
export HOSTNAME=`hostname`

docker stop $PS_NAME 2>/dev/null
docker rm $PS_NAME 2>/dev/null

docker run -it --privileged \
--env="QT_X11_NO_MITSHM=1" \
-v /tmp/.X11-unix:/tmp/.X11-unix:ro \
-v /dev:/dev:rw \
-v /Users/addps5012/OneDrive/OneDrive\ -\ koreatech.ac.kr/devOps/docker/xycar_ws:/xycar_ws \
-e DISPLAY=host.docker.internal:0 \
--hostname $(hostname) \
--group-add dialout \
--user addps5012 \
--network host \
--shm-size 4096m \
--name $PS_NAME addps5012/ros:melodic_osx bash
```

위의 내용에 대한 설명은 다음과 같다.

```null
# 해당 프로세스의 이름 지정 -> 전역 변수의 느낌
PS_NAME=ros_xycar

# X11을 사용하기 위한 Mac 터미널의 xhost 지정
xhost +
xhost + ${hostname}
export HOSTNAME=`hostname`

# 기존에 실행중인 이미지가 있다면, 종료하기(중복 실행 오류 방지)
docker stop $PS_NAME 2>/dev/null
docker rm $PS_NAME 2>/dev/null

# 도커 실행하기
docker run \
-it \													// 실행 후 접속
--privileged \											// 자원 권한을 위한 priviliged 모드로 실행
--env="QT_X11_NO_MITSHM=1" \							// X11 관련 설정
-v /tmp/.X11-unix:/tmp/.X11-unix:ro \					// X11 관련 설정 2
-v /dev:/dev:rw \										// 외부 장치(dev mapping)

# Workspace Mapping(매핑 시, 외부에서 파일 접근이 가능)
-v /Users/addps5012/OneDrive/OneDrive\ -\ koreatech.ac.kr/devOps/docker/xycar_ws:/xycar_ws \

-e DISPLAY=host.docker.internal:0 \						// X11 관련 설정
--hostname $(hostname) \								// hostname 지정
--group-add dialout \									// 외부 장치 권한 부여
--user addps5012 \										// 로그인 계정 지정
--network host \										// 네트워크 모드(host)
--shm-size 4096m \										// 메모리(RAM) 가이즈
--name $PS_NAME \										// 컨테이너 별칭 지정
addps5012/ros:melodic_osx bash							// 이미지 지정
```

Volume Mapping 기능을 통해 PC의 폴더를 컨테이너 내부의 폴더와 연결할 수 있다. /dev는 리눅스 계열에서 외부 장치를 말하며, Jetson과 같은 하드웨어에서는 센서를 연결하게 되면, 쉽게 접근해 사용할 수 있다.

**-it** 옵션은 실행 시, 해당 환경으로 접속하는 것을 의미한다. 해당 쉘을 닫을 경우에는, 컨테이너도 같이 종료되는 것에 유의해야 한다. 이를 방지하고 싶으면, **-d** 옵션을 주면 된다. 이 때에는 쉘을 종료할 때 **docker stop**을 사용해 종료해줘야 한다. 이렇게 구성한 환경에서는 다음과 같이 실행하면, ROS에 접근할 수 있다.

```null
# Shell Script 실행
sh run_xycar.sh

# 실행중인 컨테이너 접속
# docker exec -it [컨테이너 ID 또는 별칭] [접속 쉘]
docker exec -it ros_xycar bash
```

더불어 Jetson의 제작사인 NVIDIA에서는 딥러닝과 관련된 기능들을 **Jetpack**으로 제공하고 있다는 특징이 있는데, **Jetson-Container**라는 도커 이미지를 제공하고 있으므로, 이를 통해 ROS 환경을 설정하면, 온전히 이용할 수 있다. 예전에 ROS2 Humble을 Jetson Nano에서 사용하고자 설정한 이미지는 Docker Hub에 [addps5012/ros:humble_nano](https://hub.docker.com/layers/addps5012/ros/humble_nano/images/sha256-39542a08a4158c84f1d7098dc022b62d542272e033cd6eaf7902e6f90f3b1021?tab=layers)로 업로드 되어있어 바로 사용할 수 있다.

```null
PS_NAME=ros_humble

xhost +

docker stop $PS_NAME 2>/dev/null
docker rm $PS_NAME 2>/dev/null

cat /proc/device-tree/model > /tmp/nv_jetson_model

docker run -it --privileged \
--runtime nvidia \
--env="QT_X11_NO_MITSHM=1" \
-v /tmp/.X11-unix:/tmp/.X11-unix:ro \
-v /dev:/dev:rw \
-v /home/jetson/Robot_Programming/nanosaur_linetracing:/ws \
-v /tmp/argus_socket:/tmp/argus_socket \
-v /etc/enctune.conf:/etc/enctune.conf \
-v /etc/nv_tegra_release:/etc/nv_tegra_release \
-v /tmp/nv_jetson_model:/tmp/nv_jetson_model \
-e DISPLAY=$DISPLAY \
--hostname $(hostname) \
--group-add dialout \
--user root \
--network host \
--shm-size 4096m \
--name $PS_NAME addps5012/ros:humble_nano bash
```

### VSCode 개발환경 구성하기

앞에 지정한대로, Volume Mapping 기능을 사용해 도커 컨테이너 내부에서는 PC에 파일을 읽어서 사용할 수 있도록 구성했다. 따라서, 굳이 vim, nano와 같은 텍스트 에디터 외에도 VSCode나 QT Creator를 사용하면 쉽게 패키지를 사용할 수 있고, 자동완성 기능도 제공한다.

내가 사용하고 있는 ROS에 관련된 VSCode Extension은 다음과 같다.

```null
# 언어 관련 Extension
C/C++
Python

# 빌드 관련 Extension
CMake
CMake Tools
cmake-format
XML Tools
YAML
json

# ROS 관련 Extension
ROS
ROS Snippets

# 기타 Extension
Docker
```

**참고로, ROS_MASTER_URI, ROS_HOSTNAME과 같은 전역 환경변수를 설정해야 할 때가 있는데 이 때, .bashrc를 수정했다면, 이미지 commit을 저장해둬야 다음 도커 실행 때에도 동일하게 사용할 수 있다.**

## 📌 해결해야 할 문제

### OpenCV에 대한 문제

앞서 말한 것처럼 ROS를 사용해 개발할 때에는 이미지 데이터를 쓸 일이 많이 존재하고, 이미지 데이터를 처리하기 위한 OpenCV를 사용할 일이 많이 있다. OpenCV를 효율적으로 쓰기 위해서는 NPU나 GPU를 사용해야 하지만, 이를 쓰기에는 쉽지 않다. 이에 대한 해결책이 필요하다.

> M1 Mac에 OpenCV를 설치할 수 있는데, 이를 Mapping 해보는 방식으로 시도해 볼 예정이다.

### OpenGL에 대한 문제

M1 Mac에서는 OpenGL을 지원하지 않기에 Gazebo 시뮬레이터가 실행되지 않는다. 시뮬레이션 환경 구축을 위해서는 이에 대한 해결책이 필요하다.

> 최근 독립적인 Gazebo는 이를 보완한 버전이 있는거 같다. 이를 활용하는 방법을 시도해 볼 예정이다.



source : https://velog.io/@addps5012/Docker%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%9C-ROS-%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD-%EC%84%B8%ED%8C%85#rosrobot-operation-system