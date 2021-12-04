## ghost 블로그 컨테이너 생성

1. 60000 포트로 오픈합니다.
2. 업로드 데이터가 유실되지 않게 볼륨을 마운트 합니다.
3. 관리자에서 미리보기 페이지가 정상작동하도록 환경변수를 설정합니다.
4. docker-compose.yml 파일로 작성합니다.

```bash
$ docker run -d --name sangjs-ghost -e url=http://localhost:60000 -p 60000:2368 -v sangjs-ghost-data:/var/lib/ghost/content ghost
```

```
# by default, the Ghost image will use SQLite (and thus requires no separate database container)
# we have used MySQL here merely for demonstration purposes (especially environment-variable-based configuration)

version: '3.1'

services:

  ghost:
    image: ghost:4-alpine
    # 콘테이너 이름 변경
    container_name: sangjs-ghost
    restart: always
    # 볼륨 생성
    volumes:
      - ./sangjs-ghost-data:/var/lib/ghost/content
    ports:
      - 60000:2368
    environment:
      # see https://ghost.org/docs/config/#configuration-options
      database__client: mysql
      database__connection__host: db
      database__connection__user: root
      database__connection__password: example
      database__connection__database: ghost
      # this url value is just an example, and is likely wrong for your environment!
      url: http://localhost:60000
      # contrary to the default mentioned in the linked documentation, this image defaults to NODE_ENV=production (so development mode needs to be explicitly specified if desired)
      #NODE_ENV: development


  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example
```



## 방명록 배포하기

1. 62000 포트로 서버를 오픈합니다.
2. docker-compose.yml 파일로 작성합니다.

```
version: '3'
services:
  frontend:
    image: subicura/guestbook-frontend:latest
    environment:
      PORT: 8000
      GUESTBOOK_API_ADDR: backend:8000
    ports:
      - "62000:8000"
  backend:
    image: subicura/guestbook-backend:latest
    environment:
      PORT: 8000
      GUESTBOOK_DB_ADDR: mongodb:27017
    restart: always
  mongodb:
    image: mongo:4
```



## 투표 앱 생성

- 깃 리파지토리를 clone 후 진행합니다.
- 필요한 이미지를 직접 빌드하여 만듭니다.
- 여러개의 마이크로서비스를 연결합니다.

**실습내용**

1. 5개의 서비스를 하나의 docker-compose.yml로 만듭니다.
2. vote는 60001로 오픈합니다.
3. result는 60002로 오픈합니다.
4. docker-compose.yml 파일로 작성합니다.



```bash
$ git clone https://gitlab.com/44bits.io/workshop-voting.git
$ docker build -t voting-vote .
$ docker build -t voting-worker .
$ docker build -t voting-result .
```



```
# $ cd vote
# $ docker build -t voting-vote .
# $ cd worker
version: '3'

services:
  vote:
    image: voting-vote
    ports:
      - "60001:80"
  redis:
    image: redis:alpine
  worker:
    image: voting-worker
  db:
    image: postgres:9.4
    environment:
      POSTGRES_HOST_AUTH_METHOD: trust
  result:
    image: voting-result
    ports:
      - "60002:80"
```



## 실시간 채팅 앱 생성

```
version: '3.6'

services:
  chatapp:
    image: chatapp
    ports:
    - "60003:8080"
  postgres:
    image: postgres
    restart: always
    volumes:
    - db_data:/var/lib/postgresql/data
    environment:
      POSTGRES_HOST_AUTH_METHOD: trust
  graphql-engine:
    image: hasura/graphql-engine:latest.cli-migrations
    ports:
    - "60004:8080"
    depends_on:
    - "postgres"
    volumes:
    - ./hasura/migrations:/hasura-migrations
    restart: always
    environment:
      HASURA_GRAPHQL_DATABASE_URL: postgres://postgres:@postgres:5432/postgres
      HASURA_GRAPHQL_ENABLE_CONSOLE: "true" # set to "false" to disable console
      HASURA_GRAPHQL_ENABLED_LOG_TYPES: startup, http-log, webhook-log, websocket-log, query-log
      ## uncomment next line to set an admin secret
      # HASURA_GRAPHQL_ADMIN_SECRET: myadminsecretkey
volumes:
  db_data:
```

