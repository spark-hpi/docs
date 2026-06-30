---
up:
  - "[[How To Home Server]]"
---

## Handy Docker Commands

*All `docker compose` commands expect you to be in the same directory as the `compose.yml` file.*
For more info on `compose.yml` files you can read the official [docs](https://docs.docker.com/reference/compose-file/).

```bash
docker compose down # Stop the container(s)
docker compose logs -f -n 500 # Show the last 500 log lines of all containers in the current stack (The -n flag is recommended because if left, it will show all logs, which can grow quite large over time)
docker image prune -a -f # Removes images that are not in use (all containers that are down)
docker compose pull # Pulls the (latest) container images.
```

## Multiple Container In One File

It is possible to have multiple containers in the same `compose.yml` file. You can start them all at once and they share an internal virtual network. This is quite handy because you can combine a database, web server and much more in the same file, which can communicate directly with next to no overhead. It is also possible to copy this file to a new machine and spin up a new deployment without checking dependencies.

## Deleting containers

Docker container are designed to be deleted. User data is stored on disk or in volumes which are persistent. However, this means that you will have to ensure that all configuration is stored in volumes.

## Updating containers

This might be counter intuitive at first , but you do not update, you replace containers with its newer version. Using this approach you are guarantied to always have a working version after the update. Your data is **not** deleted by this process **but always take a backup to be safe**.
