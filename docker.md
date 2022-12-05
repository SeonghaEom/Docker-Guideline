# 도커 이미지

Date: December 1, 2022

### 볼륨(O) or Bind mounts

```bash
$ docker volumne create my_volume
$ docker volume ls
```

출처

[link](https://www.daleseo.com/docker-volumes-bind-mounts/) 

[https://bentist.tistory.com/79#:~:text=도커 공식 문서에서 권장,volumes%2F~ 에 저장된다](https://bentist.tistory.com/79#:~:text=%EB%8F%84%EC%BB%A4%20%EA%B3%B5%EC%8B%9D%20%EB%AC%B8%EC%84%9C%EC%97%90%EC%84%9C%20%EA%B6%8C%EC%9E%A5,volumes%2F~%20%EC%97%90%20%EC%A0%80%EC%9E%A5%EB%90%9C%EB%8B%A4).

[https://docs.docker.com/storage/volumes/#back-up-restore-or-migrate-data-volumes](https://docs.docker.com/storage/volumes/#back-up-restore-or-migrate-data-volumes)

### 이미지와 컨테이너 생성

![Untitled](%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%8B%E1%85%B5%E1%84%86%E1%85%B5%E1%84%8C%E1%85%B5%20fbca1d28aa1d4171aca82773918b3b65/Untitled.png)

![Untitled](%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%8B%E1%85%B5%E1%84%86%E1%85%B5%E1%84%8C%E1%85%B5%20fbca1d28aa1d4171aca82773918b3b65/Untitled%201.png)

1. nvidia-docker 이미지 다운로드, 컨테이너 실행

```bash
# 도커에서 nvidia runtime을 잡기위한 패키지 
sudo apt-get install nvidia-container-runtime

# 패키지 설치 확인
docker info|grep -i runtime
 Runtimes: nvidia runc
 Default Runtime: runc

# nvidia-docker 설치
# Add the package repositories
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \
  sudo apt-key add -
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
  sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update

# Install nvidia-docker2 and reload the Docker daemon configuration
sudo apt-get install -y nvidia-docker2
sudo pkill -SIGHUP dockerd

# 도커에서 실행중인 Process 확인
docker ps
```

1. 쿠다 드라이버 잡히는지 확인

```bash
##### 기본적인 이미지 다운로드
docker pull ubuntu 
docker run -it --name test ubuntu bash
#####

# Test nvidia-smi with the latest official CUDA image
docker run --runtime=nvidia --gpus 0 nvidia/cuda:10.0-devel nvidia-smi
```

![Untitled](%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%8B%E1%85%B5%E1%84%86%E1%85%B5%E1%84%8C%E1%85%B5%20fbca1d28aa1d4171aca82773918b3b65/Untitled%202.png)

1. 컨테이너 실행 (볼륨 지정, gpu 지정)

```bash
# Start image with volume
# docker run –v <볼륨명>:<컨테이너 내 경로> <이미지>
## gpu 지정
docker run -v my_volumn:/home -it --runtime=nvidia --gpus 0 nvidia/cuda:10.0-devel
```

![Untitled](%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%8B%E1%85%B5%E1%84%86%E1%85%B5%E1%84%8C%E1%85%B5%20fbca1d28aa1d4171aca82773918b3b65/Untitled%203.png)

- 초기 컨테이너 세팅
    
    3.1 실행된 컨테이너에 이것저것 설치 (이미 설치되어있으니 SKIP)
    
    ```bash
    # apt 갱신
    $ apt update
    
    # git 설치
    $ apt install git
    
    # python3, pip 설치
    $ apt install python-pip
    $ apt upgrade python3
    $ pip install --upgrade setuptools
    $ pip install --upgrade pip
    
    # 만약에 Python설치가 안되어있다면 특정 버전 설치
    $ apt install python3.9
    
    # alternative 등록
    $ update-alternatives --install /usr/bin/python python /usr/bin/python3.9 1
    
    # 버전을 바꾸고 싶다면
    $ apt install python3.10
    $ update-alternatives --install /usr/bin/python python /usr/bin/python3.10 2
    $ update-alternatives --config python
    There are 3 choices for the alternative python (providing /usr/bin/python).
    
      Selection    Path                 Priority   Status
    ------------------------------------------------------------
    * 0            /usr/bin/python3.10   2         auto mode
      1            /usr/bin/python3.10   2         manual mode
      2            /usr/bin/python3.8    1         manual mode
      3            /usr/bin/python3.9    2         manual mode
    ```
    
    3.2 플젝 코드 다운로드 및 패키지 설치
    
    ```bash
    
    git clone https://github.com/sungnyun/epidemic-modeling --single-branch -b submission
    cd epidemic-modeling
    pip install -r requirements.txt
    ```
    
    ![Untitled](%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%8B%E1%85%B5%E1%84%86%E1%85%B5%E1%84%8C%E1%85%B5%20fbca1d28aa1d4171aca82773918b3b65/Untitled%204.png)
    
1. 확인 (home 에 볼륨 마운트 되어있으니 home 내에서 플젝 코드 포함해서 모든 save할 데이터 저장)

```bash
cd home/epidemic-modeling
# 예시로 run.sh 돌리기, 이거는 예제 데이터하고 output 같이 주기 위함.
bash run.sh
...
# start date은 2020-03-01, end date은 2020-03-31로 하고, keyword하고 countries는 알아서 선택해서 넣어주면 될듯.
#다 잘 도는 거 확인하고 output, figure 잘 들어가 있는거 확인
ls detect/workspace/detection/type_False
ls predict/SAICA/fig/
```

![Untitled](%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%8B%E1%85%B5%E1%84%86%E1%85%B5%E1%84%8C%E1%85%B5%20fbca1d28aa1d4171aca82773918b3b65/Untitled%205.png)

![Untitled](%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%8B%E1%85%B5%E1%84%86%E1%85%B5%E1%84%8C%E1%85%B5%20fbca1d28aa1d4171aca82773918b3b65/Untitled%206.png)

![Untitled](%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%8B%E1%85%B5%E1%84%86%E1%85%B5%E1%84%8C%E1%85%B5%20fbca1d28aa1d4171aca82773918b3b65/Untitled%207.png)

### 컨테이너 commit

이것저것 설치된 컨테이너를 commit하여 이미지 생성(백업)

```bash

# 컨테이너 bash 연결 종료
$ exit

$ sudo docker system df

# commit!
# docker commit <컨테이너명> <리포지토리>:<태그>
$ docker container ls
$ docker commit my-ubuntu monadk:ubuntu-git
sha256:e12df11d15f7594831b079ea0118a0f78faf2b38110f7aa9fe63f9c9ae26eb38

# 생성되었는지 확인
# monadk 리포지토리, ubuntu-git이라는 태그로 이미지 생성되어 있음
$ docker images
```

![Untitled](%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%8B%E1%85%B5%E1%84%86%E1%85%B5%E1%84%8C%E1%85%B5%20fbca1d28aa1d4171aca82773918b3b65/Untitled%208.png)

![Untitled](%E1%84%83%E1%85%A9%E1%84%8F%E1%85%A5%20%E1%84%8B%E1%85%B5%E1%84%86%E1%85%B5%E1%84%8C%E1%85%B5%20fbca1d28aa1d4171aca82773918b3b65/Untitled%209.png)

### 컨테이너 중단 및 삭제

```bash
# 컨테이너 중단
docker stop [container id/name]
#삭제
docker kill [container id/name]
```

### 이미지 중단 및 삭제

```bash
#docker 이미지 리스트
docker images

# 이미지 삭제

docker rmi [image id]
# 하위 컨테이너 삭제
docker rm -f $(docker ps -aq --filter ancestor=hello-world)
```

### 도커 이미지 ↔  tar

```bash
# 이미지 -> tar파일
docker save -o submission.tar nvidia/cuda:10.0-devel

# tar파일 -> 이미지
docker load -i submission.tar
```

### Dockerfile Export하기 (처음부터 생성할때)

instruction이 명시되어있는 파일, 이 파일을 기반으로 처음부터 이미지 생성 가능.

출처

[link1](https://velog.io/@monadk/Docker-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EB%A7%8C%EB%93%A4%EA%B8%B0)

[link2](https://ajdkfl6445.gitbook.io/study/devops/docker/basic-command)

[link3](https://jybaek.tistory.com/796)

[link4](https://github.com/NVIDIA/nvidia-docker)

[link5](https://www.daleseo.com/docker-run/)

[link6](https://stackoverflow.com/questions/23935141/how-to-copy-docker-images-from-one-host-to-another-without-using-a-repository)
