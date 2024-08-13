---
layout: post
title: Nvidia Docker 설치
---

### [1. Nvidia Docker 설치](https://lolz0309.tistory.com/8#--%--Nvidia%--Docker%--%EC%--%A-%EC%B-%--)

1) Repo 설정과 GPG키를 설치 한다.

```shell
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
      && curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
      && curl -s -L https://nvidia.github.io/libnvidia-container/$distribution/libnvidia-container.list | \
            sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
            sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

2) Nvidia Docker 설치

```shell
sudo apt-get update
sudo apt-get install -y nvidia-docker2
```

3. docker pull

![image-20240813125337898](https://github.com/leelang7/leelang7.github.io/blob/master/assets/image-20240813125337898.png)



4. 도커 컨네이너 진입

   docker run --gpus all -it nvidia/cuda:11.6.2-base-ubuntu20.04 /bin/bash

   

5. nvidia-smi 명렁어로 도커 컨테이너 내부에서 gpu 설정 확인

   ![image-20240813125535071](https://github.com/leelang7/leelang7.github.io/blob/master/assets/image-20240813125535071.png)



6. 도커 컨테이너에서 

   apt update

   apt install wget

7. 아나콘다 다운로드

   wget https://repo.anaconda.com/archive/Anaconda3-2021.05-Linux-x86_64.sh 

   sh Anaconda3-2021.05-Linux-x86_64.sh 

8. 환경설정 적용

   ```
   echo 'export PATH=/root/anaconda3/bin:$PATH' >> ~/.bashrc
   ```

   ```
   echo "source /root/anaconda3/etc/profile.d/conda.sh" >> ~/.bashrc
   ```

   ```
   source ~/.bashrc
   ```

* 정리중
  
docker export -o {출력파일명.tar} {컨테이너명 or name} : 스냅샷

docker import {파일명.tar}

docker commit {컨테이너명 or name} {이미지파일명}

docker save -o {출력파일명.tar} {컨테이너명 or name}  : 레이어 정보 까지 포함

docker load -i {파일명.tar}

docker images : 도커들 정보출력(base이미지, commit한 이미지 등)
