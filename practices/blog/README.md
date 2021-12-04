## ghost 블로그 컨테이너 생성

1. 60000 포트로 오픈합니다.
2. 업로드 데이터가 유실되지 않게 볼륨을 마운트 합니다.
3. 관리자에서 미리보기 페이지가 정상작동하도록 환경변수를 설정합니다.
4. docker-compose.yml 파일로 작성합니다.

```bash
$ docker run -d --name sangjs-ghost -e url=http://localhost:60000 -p 60000:2368 -v sangjs-ghost-data:/var/lib/ghost/content ghost
```

```sh
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

