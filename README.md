# test_sample_test_runner

A test runner to execute system tests

## Test runner image

In order to build the test runner image execute the following command:
```bash
docker build -f Dockerfile -t test_sample_test_runner:local .
```

In order to delete the test runner image execute the following command:
```bash
docker rmi test_sample_test_runner:local
```

## Test runner behavior

Test runner needs to be part of a compose file. For example:

```yaml
version: '3.3'
services:
  test_sample_server:
    image: test_sample_server:local
    ports:
    - "8080:8080"
    command: script/run
  test_sample_test_runner:
    image: test_sample_test_runner:local
    environment:
      - SERVER_HOST=test_sample_server
      - SERVER_PORT=8080
    tty: true
    volumes:
    - ./tests:/test_sample_source/tests
```

(Source: https://github.com/fmihaich/test_sample_server/blob/master/system_tests.yml)

Test runner will:

- Install requirements located in ``tests/requirements.txt``
- Run all tests in ``tests/system/*/folder`` using behave.

Therefore, the repository where test runner will run need to:
- Specify system tests requirements in ``tests/requirements.txt``
- Define the system tests in ``tests/system`` folder.

As an example, take a look to this repo: https://github.com/fmihaich/test_sample_server

## Running test

Suppose the compose file is named ``system_tests.yml``, then execute the following command to run test environment:

```bash
docker-compose -f system_tests.yml up &
```

After that, to run system test execute:

```bash
docker-compose -f system_tests.yml exec test_sample_test_runner script/run
```

To edit and work on system tests run the following command:

```bash
docker-compose -f system_tests.yml exec test_sample_test_runner /bin/bash
```

To down the docker-compose environment run:

```bash
docker-compose -f system_tests.yml down -v
```
