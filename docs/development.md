---
title: Development
---

# Installing Bubify for Development

## Prerequsites

You need to have Docker installed and at least 4 GB of memory.

### macOS
If you use macOS and can't use Docker Desktop due to their updated licence you may use [OrbStack](https://orbstack.dev/) instead.

## Installing

Clone the project:
``` bash
git clone --recurse-submodules https://github.com/bubify/bubify-docker.git
```

Then go into the `bubify-docker` folder get the latest updates
``` bash
cd bubify-docker && make update
```

Additionally, copy the environment template for development. There should be no need to change them.
``` bash
cp .env.development.template .env.development
```

You can now build the docker compose project and start it as a deamon. The frontend can be accessed at [http://localhost:3000](http://localhost:3000).
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

Note that both will stop your containers, so you need to type `make up-development` again.

## Run Tests Using Docker

To run the test suite in a Docker container, choose one of the following build rules, depending on what testing level you are targeting (both, unit or integration):

``` bash
make build-testing
```
``` bash
make build-testing-unit
```
``` bash
make build-testing-integration
```

Launch container and start tests:
``` bash
make up-testing
```

Test results are printed directly into the Logs page for the `bubify-backend-testing` container and may also be examined afterwards in the your local `backend/test-reports/` folder. Unit test results are found in the `surefire-reports` folder, integration tests in `failsafe-reports` folder and code coverage for the executed test scope in `jacoco` folder.

Stop test container:
``` bash
make down-testing
```

### Reset test suite

If something goes terribly wrong you might want to reset everything (including your Docker containers) with
``` bash
make clean
```

Note that this will stop all containers, so you need to type `make up-testing` again.

### Run tests locally
If you prefer you can also run the tests directly in the `backend` folder.

To run the full test suite on your machine change directory to `./backend` and:
``` bash
make test
```

To run units test only on your machine:
``` bash
make test-unit
```

To run integration tests on your machine:
``` bash
make test-integration
```

Test results are printed directly into your terminal and may also be examined afterwards in your local `backend/target/` folder. Unit test results are found in the `surefire-reports` folder, integration tests in `failsafe-reports` folder and code coverage for the executed test scope in `jacoco` folder.

### Contribute by adding more tests

The test suite currently covers all classes in the `models` and `repository` folders for unit tests. For integration tests, some classes in the `controllers` folder are targeted. To contribute and expand the suite to cover all APIs, navigate to the `backend/src/test/java/com/uu/au/api/` folder and create a new test class. Good to know before you get started:

- Class names must include `IT` for them to be identified and run as integration tests.
- The `@DirtiesContext` annotation ensures the Spring Boot app and database are restarted between each test. Note that the DB is always wiped between tests.
- Make sure to copy the `setup` method from another IT class and inject the `TestHelper` in your class. These two together ensure each test is initiated by creating a course, user, and retrieving the necessary token to authenticate to Bubify.
- `TestHelper` also helps you populate and retrieve data from the database through API calls stored in various methods. Feel free to use these and expand the `TestHelper` with more methods as necessary.