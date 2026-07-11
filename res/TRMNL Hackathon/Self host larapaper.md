## Deploy your own Larapaper

To self-host larapaper with serverless transforms, you have to use [our fork](https://github.com/spark-hpi/larapaper) of [larapaper](https://github.com/usetrmnl/larapaper). We will once again use [docker compose](../How%20to%20Home%20Server/03%20Installing%20Docker.md) for this.

We are currently working on merging our fork back into the main repo, but this might take a while. Check for updates on this topic.

Begin by creating a `compose.yaml` with the following contents:

```yml
services:
  larapaper:
    container_name: larapaper
    image: ghcr.io/spark-hpi/larapaper:latest
    ports:
      - "4567:8080"
    environment:
      - APP_KEY=base64:<TODO>
      - PHP_OPCACHE_ENABLE=1
      - TRMNL_PROXY_REFRESH_MINUTES=15
      - DB_DATABASE=database/storage/database.sqlite
      - TRANSFORM_RUNNER_URL=transform-runner:3000
      # - MULTI_USER_MODE=1 # if you are planning to use larapaper with multiple users
    volumes:
      - database:/var/www/html/database/storage
      - storage:/var/www/html/storage/app/public/images/generated
    restart: unless-stopped

  transform-runner:
    container_name: transform-runner
    image: ghcr.io/spark-hpi/larapaper-serverless-runner:latest
    security_opt: [seccomp=unconfined]
    environment:
      - TRANSFORM_TIMEOUT=30 # maximum time a transform may run
      - TRANSFORM_MEMORY_LIMIT=134217728 # (128MB) the amount of memory a single transform may use, killed if the limit is exceeded
      # - FORCE_HTTPS=1 # if running behind a reverse proxy

volumes:
  database:
  storage:
```

Replace `<TODO>` inside the `APP_KEY` with the output of the command `openssl rand -hex 16 | base64`.

For more information, refer to Larapapers [README](https://github.com/spark-hpi/larapaper/blob/main/README.md), create an issue or ask us if you have questions. Pull requests with improvements are also always welcome!
