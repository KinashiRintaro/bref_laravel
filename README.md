# 経緯

以下はLaravel10の表示、マイグレーションまでできていたリポジトリです。
[https://github.com/KinashiRintaro/Laravel10_bref](https://github.com/KinashiRintaro/Laravel10_bref)

makefileを試すために一旦上記ディレクトリを削除→再度cloneを試したのですが、
上記のリポジトリではLaravelの表示さえできなくなってしまいました。。

<details>
<summary>詳細</summary>

phpコンテナが立ち上がらなくなってしまいました。
以前は以下の書き方で動いたのですが
```
  php:
    build: ./infra/php
    volumes:
      - ./src:/var/task:ro
  web:
    image: bref/fpm-dev-gateway
    ports:
      - '8080:80'
    volumes:
      - ./src:/var/task
    depends_on:
      - php
    environment:
      HANDLER: public/index.php
      DOCUMENT_ROOT: public
```

HANDLERを設定する箇所がphpコンテナに無いと怒られるようになってしまいました。
```
  php:
    build: ./infra/php
    volumes:
      - ./src:/var/task:ro
    environment:
      HANDLER: public/index.php
      DOCUMENT_ROOT: public
  web:
    image: bref/fpm-dev-gateway
    ports:
      - '8080:80'
    volumes:
      - ./src:/var/task
    depends_on:
      - php
```

</details>

よって本リポジトリで、最小構成にてやり直してみたのですが、htmlの表示ができない段階で躓いております。。
（削除前に上記リポジトリで試した際は`index.html`も`phpinfo.php`も動作できておりました。）

```
// コンテナを起動
docker compose up -d
```
http://localhost:8080/index.html
<img width="1603" alt="スクリーンショット 2023-03-21 16 31 06" src="https://user-images.githubusercontent.com/109059339/226542077-a402a4ad-0bfa-4908-84e6-dc0d38c31208.png">

http://localhost:8080/phpinfo.php
<img width="1601" alt="スクリーンショット 2023-03-21 16 31 28" src="https://user-images.githubusercontent.com/109059339/226542127-fdf4af84-debc-49e8-bd25-4dc6fa34ec71.png">


webコンテナに以下のようなログが出ますが、解決できずにいます。。
```
172.27.0.1 - - [21/Mar/2023:07:35:29 +0000] "GET /phpinfo.php HTTP/1.1" 502 559 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/111.0.0.0 Safari/537.36" "-"
2023/03/21 07:35:29 [error] 10#10: *1 connect() failed (111: Connection refused) while connecting to upstream, client: 172.27.0.1, server: php-docker.local, request: "GET /phpinfo.php HTTP/1.1", upstream: "fastcgi://172.27.0.2:9001", host: "localhost:8080"
172.27.0.1 - - [21/Mar/2023:07:35:38 +0000] "GET /index.html HTTP/1.1" 502 559 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/111.0.0.0 Safari/537.36" "-"
2023/03/21 07:35:38 [error] 10#10: *1 connect() failed (111: Connection refused) while connecting to upstream, client: 172.27.0.1, server: php-docker.local, request: "GET /index.html HTTP/1.1", upstream: "fastcgi://172.27.0.2:9001", host: "localhost:8080"
```
