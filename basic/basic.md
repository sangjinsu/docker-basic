# 도커 설치부터 실행까지

- docker CLI 는 도커 호스트에 명령을 전달하고 결과를 받아서 출력한다 

## 기본 명령어

```bash
$ docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]
```

|   flag    |                        content                         |
| :-------: | :----------------------------------------------------: |
|    -d     |            detached mode (백그라운드 모드)             |
|    -p     |            호스트와 컨테이너의 포트를 연결             |
|    -v     |          호스트와 컨테이너의 디렉토리를 연결           |
|    -e     |          컨테이너 내에서 사용할 환경변수 설정          |
|  --name   |                   컨테이너 이름 설정                   |
|   --rm    |           프로세스 종료시 컨테이너 자동 제거           |
|    -it    | -i와 -t를 동시에 사용한 것으로 터미널 입력을 위한 옵션 |
| --network |                     네트워크 연결                      |

```bash
$ docker run ubuntu:20.04
```

- `run` - 사용 이미지가 저장되어있는지 확인하고 없으면 pull 하고 create 하고 start 한다 

- 컨테이너는 프로세스로 실행 중인 프로세스가 없으면 컨테이너는 종료된다 

- `-it` 옵션을 입력하지 않아 리눅스 쉘 실행이 되지않아 종료되었다

```bash
$ docker run --rm -it ubuntu:20.04 /bin/sh
```

- 컨테이너 내부 `sh` 을 실행하고 키보드 입력을 위해 -it 옵션을 준다 

```bash
$ docker run --rm -it centos:8 /bin/sh
```

- centOS
- Alpine 이라는 초소형 이미지를 사용할 수 있다

```bash
$ docker run --rm -p 5678:5678 hashicorp/http-echo -text="hello world"
```

- 웹 어플리케이션 실행하기
- detached mode 백그라운드 모드로 실행하기 위해 -d 옵션을 추가하고 -p 옵션을 추가하여 컨테이너 포트를 호스트 포스트로 연결하였다

```bash
$ docker run --rm -p 1234:6379 redis
```

- redis db

```bash
docker run -d -p 3308:3308 -e MYSQL_ALLOW_EMPTY_PASSWORD=true --name mysql mysql:5.7
```

- mysql 설치 => 포트 번호 3306 에서 3308로 변경

```bash
$ docker exec -it mysql mysql // 접속
$ create database wp CHARACTER SET utf8;
$ grant all privileges on wp.* to wp@'%' identified by 'wp';
$ flush privileges;
$ quit
```

- `exec` : run 명령어와 다르게 실행중인 도커 컨테이너에 접속할 때 사용한다.

```bash
// 워드프레스 블로그 실행
$ docker run -d -p 8080:80 \
	-e WORDPRESS_DB_HOST=host.docker.internal \
	-e WORDPRESS_DB_NAME=wp \
	-e WORDPRESS_DB_USER=wp \
	-e WORDPRESS_DB_PASSWORD=wp \
	wordpress
```

## 도커 기본 명령어

- ps 

  ```bash
  $ docker ps
  $ docker ps -a // 중지된 컨테이너도 확인
  ```

- stop

  ```bash
  $ docker stop [OPTIONS] CONTAINER [CONTAINER...]
  ```

  - 실행 중인 컨테이너 중지 명령어
  - 여러 컨테이너를 한번에 중지할 수 있다 

-  rm

  ```bash
  $ docker rm [OPTIONS] CONTAINER [CONTAINER...]
  ```

  - 종료된 컨테이너를 완전히 제거한다 

- logs

  ```bash
  $ docker logs [OPTIONS] CONTAINER
  ```

  - 컨테이너 동작을 로그를 통해 확인한다
  - `-f` : 로그 생성시 로그를 추가적으로 보여준다 

- images

  ```bash
  $ docker images [OPTIONS] [REPOSITORY[:TAG]]
  ```

  - 도커 이미지 목록을 보여준다 

- pull

  ```bash
  $ docker pull [OPTIONS] NAME[:TAG|@DIGEST]
  ```

  - 도커 이미지 다운로드

- rmi

  ```bash
  $ docker rmi [OPTIONS] IMAGE [IMAGE...]
  ```

  - 도커 이미지 삭제 
  - 실행 중인 컨테이너는 삭제되지 않는다

- network 

  ```bash
  $ docker network create [OPTIONS] NETWORK
  ```

  - 도커 컨테이너끼리 이름으로 통신할 수 있는 가상 네트워크를 생상한다

  ```bash
  $ docker network connect [OPTIONS] NETWORK CONTAINER
  ```

  - 기존 컨테이너에 네트워크를 추가한다 

- volumn mount -v

  데이터를 볼륨에 저장하면서 컨테이너를 삭제하고 다시 실행했을 때 데이터가 사라지지 않고 그대로 연결된다

  - 더 알아봐야하는 명령어이다

## docker compose

- docker-compose.yml 을 먼저 만든다
- 실행

```bash
$ docker-compose up -d
```

- 종료

```bash
$ docker-compose down
```

