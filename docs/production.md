---
title: Production
---
# Deploying Bubify for Production

## Prerequsites

You need to have Docker installed and at least 4 GB of memory during building. We recommend only deploying on Linux hosts for performance reasons.

## Installing

Clone the project:
``` bash
git clone --recurse-submodules https://github.com/bubify/bubify-docker.git
```

Then go into the `docker` folder get the latest updates
``` bash
cd docker && make update
```

Additionally, copy the environment template for production.
``` bash
cp .env.production.template .env.production
```

In `.env.production` you need to change all occurrences of `example.com` to your own domain. For `https` you have three choices: (1) use own certificate, (2) use `certbot` for using free Let’s Encrypt certificates or (3) let another reverse-proxy handle encryption.

Here is how to resolve these strategies:

  1. In `docker-compose.yml` uncomment and change `- my_domain.crt:/home/bubify/ssl.crt` and `- my_domain.ssl:/home/bubify/ssl.key` to point to your own
  2. [Here](https://www.nginx.com/blog/using-free-ssltls-certificates-from-lets-encrypt-with-nginx/) is one example of how `certbot` can be used
  3. Should work out of the box. Just point the other reverse proxy at port 80 on your Docker host.

Unless you uncomment and link in your own certificates the default generated certificate is used which is valid for `localhost`. This is to allow ease of use when testing production environment on a developer machine.

After you have resolved the certificate can now build the docker compose project and start it as a deamon
``` bash
make build-production
make up-production
```

You may continuously track the logs for the frontend and backend

``` bash
docker logs -f bubify-backend
docker logs -f bubify-frontend
```

You may now run the initial setup wizard. But note that the backend server must have finished its initial initalization.
``` bash
make setup
```

You may also populate with users and achievements. Replace `backend/toolbox/achievements.csv` and `backend/toolbox/users.csv` with your own configuration.
``` bash
make add-user
make add-achievement
```

!!! warning

    Do not run `add-user` or `add-achievement` before `setup` as this might corrupt your installation state

If achievements or new users are not appearing in the frontend try clearing your browser's cache and restarting the backend with

If you wish to stop the containers you can do so with
``` bash
make down-production
```

## Cleaning

Only do manual cleaning. Do not use the clean commands as these will pruge the database.
