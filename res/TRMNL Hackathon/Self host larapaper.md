## Deploy your own Larapaper

To self host larapaper with serverless transforms you have to use [our fork](https://github.com/spark-hpi/larapaper) of [larapaper](https://github.com/usetrmnl/larapaper). To deploy it on your server we will once again use [docker compose](../How to Home Server/03 Installing Docker.md).

We are currently working on merging our fork back into the main repo but this might take a while. Just check for updates on this topic.

To download and start larapaper just create a `compose.yml` with the following content and run `docker compose up -d`

```yml
services:
  larapaper:
    container_name: larapaper
    image: ghcr.io/spark-hpi/larapaper:latest
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
    image: ghcr.io/spark-hpi/larapaper-serverless-runner:latest
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
