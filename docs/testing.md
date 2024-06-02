---
title: Testing
---

# Installing Bubify for Testing

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

Then go into the `bubify-docker` folder get the latest updates
``` bash
cd bubify-docker && make update
```
## Run tests in a Docker container

To run the suite in a Docker container, choose one of the following build rules, depending on what testing level you are targeting (both, unit or integration):

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

## Cleaning

If something goes terribly wrong you might want to reset everything with
``` bash
make clean
```

Note that this will stop the container, so you need to type `make up-testing` again.

## Run tests locally
To run the full test suite on your Linux machine:
``` bash
make test
```

To run units test only on your Linux machine:
``` bash
make test-unit
```

To run integration tests on your Linux machine:
``` bash
make test-integration
```

Test results are printed directly into your terminal and may also be examined afterwards in your local `backend/target/` folder. Unit test results are found in the `surefire-reports` folder, integration tests in `failsafe-reports` folder and code coverage for the executed test scope in `jacoco` folder.

## Contribute by adding more tests

The test suite currently covers all classes in the `models` and `repository` folders for unit tests. For integration tests, some classes in the `controllers` folder are targeted. To contribute and expand the suite to cover all APIs, navigate to the `backend/src/test/java/com/uu/au/api/` folder and create a new test class. Good to know before you get started:

- Class names must include `IT` for them to be identified and run as integration tests.
- The `@DirtiesContext` annotation ensures the Spring Boot app and database are restarted between each test. Note that the DB is always wiped between tests.
- Make sure to copy the `setup` method from another IT class and inject the `TestHelper` in your class. These two together ensure each test is initiated by creating a course, user, and retrieving the necessary token to authenticate to Bubify.
- `TestHelper` also helps you populate and retrieve data from the database through API calls stored in various methods. Feel free to use these and expand the `TestHelper` with more methods as necessary.