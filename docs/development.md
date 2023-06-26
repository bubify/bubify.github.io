---
title: Development
---

# Installing Bubify for Development

## Prerequsites

You need to have Docker installed and at least 4 GB of memory.

### macOS
If you use macOS and can't use Docker Desktop due to their updated licence you may use [colima](https://github.com/abiosoft/colima) instead.

!!! warning

    Be sure to start with `colima start --memory 4` as the default of 2 GB might not be enough. Also note that you may increase the number of CPU that colima is using.

## Installing

Clone the project:
``` bash
git clone --recurse-submodules https://github.com/bubify/bubify-docker.git
```

Then go into the `docker` folder get the latest updates
``` bash
cd docker && make update
```

Additionally, copy the environment template for development. There should be no need to change them.
``` bash
cp .env.development.template .env.development
```

You can now build the docker compose project and start it as a deamon
``` bash
make build-development
make up-development
```

You may continuously track the logs for the frontend and backend

``` bash
docker logs -f bubify-backend
docker logs -f bubify-frontend
```
!!! tip

    Setup a `tmux` session that tracks the logs in split screen

You may now run the initial setup wizard. But note that the backend server must have finished its initial initalization.
``` bash
make setup
```

You may also optionally populate with development users (to avoid the need for using CAS) and sample achievements.
``` bash
make add-user
make add-achievement
```

Note that data that is used is found in `backend/toolbox/achievements.csv` and `backend/toolbox/users.csv`.

!!! warning

    Do not run `add-user` or `add-achievement` before `setup` as this might corrupt your installation state

If achievements or new users are not appearing in the frontend try clearing your browser's cache and restarting the backend with

``` bash
make restart-backend
```

If you wish to stop the containers you can do so with
``` bash
make down-development
```

## Cleaning

If something goes terribly wrong you might want to reset everything with
``` bash
make clean
```

If you just want to reset the database you may instead use
``` bash
make clean-db
```

Note that both will stop your containers, so you need to type `docker compose up -d` again.
