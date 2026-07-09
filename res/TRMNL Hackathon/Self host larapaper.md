## Deploy your own Larapaper

To self host larapaper with serverless transforms you have to use [our fork](https://github.com/spark-hpi/larapaper) of [larapaper](https://github.com/usetrmnl/larapaper). To deploy it on your server we will once again use [docker compose](../How to Home Server/03 Installing Docker.md).

In this case you need to build the containers from source (the build section in the yml file). This is simply done by running `docker compose up` while in the same directory as the `compose.yml` file but might take a while. Now it should build an run without any problems and only rebuild on changes in the repo.

`compose.yml`:

```yml
services:
  larapaper:
    container_name: larapaper
    build: https://github.com/spark-hpi/larapaper.git#main
    ports:
      - "4567:8080"
    environment:
      - APP_KEY=base64:zzPXBQPlgn0NHwVBTVG0B//8P/PVwVnBp2gk0ZWR0+k=
      - PHP_OPCACHE_ENABLE=1
      - TRMNL_PROXY_REFRESH_MINUTES=15
      - DB_DATABASE=database/storage/database.sqlite
      - TRANSFORM_RUNNER_URL=transform-runner:3000
      - TRANSFORM_TIMEOUT_SECONDS=30
    volumes:
      - database:/var/www/html/database/storage
      - storage:/var/www/html/storage/app/public/images/generated
    restart: unless-stopped
    #platform: "linux/arm64/v8"

  transform-runner:
    container_name: transform-runner
    build: https://github.com/spark-hpi/larapaper-serverless-runner.git#slopfest
    security_opt:
      - seccomp=unconfined
      - no-new-privileges=false
      - apparmor=unconfined
    environment:
      TRANSFORM_TIMEOUT: 30
      TRANSFORM_MEMORY_LIMIT: 134217728

volumes:
  database:
  storage:
```

If you plan to use it in production you need to generate a new `APP_KEY` with `docker exec -it larapaper php artisan key:generate --show` while the container is running.

For more info read the [readme](https://github.com/spark-hpi/larapaper/blob/main/README.md) and create an issue or pull request if you have questions / improvements!
