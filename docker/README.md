Running ACA-Py with the kafka_queue Plugin
===========================================

## Quickstart

To build the container, run from project root:

```sh
$ docker build -f docker/Dockerfile -t acapy-kafka_queue .
```

To start an agent using the default configuration:

```sh
$ docker run -it -p 3000:3000 -p 3001:3001 --rm acapy-kafka_queue
```

For development purposes, it is often useful to use local versions of the code
rather than rebuilding a new container with the changes.

To start an agent using the default configuration and local versions of ACA-Py
and/or the kafka_queue plugin (paths must be adapted to your environment):

```sh
$ docker run -it -p 3000:3000 -p 3001:3001 --rm \
	-v ../aries-cloudagent-python/aries_cloudagent:/home/indy/site-packages/aries_cloudagent:z \
	-v ../aries-acapy-kafka_queue/kafka_queue:/home/indy/aries-acapy-plugin-kafka_queue/kafka_queue:z \
	acapy-kafka_queue
```
## Services

- Kafka & Zookeeper: Kafka exposed externally in `localhost:29092` and in the docker-compose network in `kafka:9092`.
- ACA-PY + kafka plugin: Exposed externally in `localhost:3001`.
- Restproxy: Exposed in the docker-compose network in `restproxy:8086`.
- RestWrapper: Exposed externally in `localhost:8080` the Open APIs spec is available in `http://localhost:8080/docs`

## Adjusting Parameters

For each of the commands listed below, ensure the image has been built:

```sh
$ docker build -t acapy-kafka_queue .
```

#### Listing configuration options

To see a list of configuration options, run:

```sh
$ docker run -it --rm acapy-kafka_queue start --help
```

#### Command line

The entry point for the container image allows adding configuration options on
startup. When no command line options are given, the following command is run
by default in the container:

```sh
$ aca-py start --arg-file default.yml
```

To add your own configuration (such as adjusting the Admin API to run on a
different port), while keeping the defaults:

```sh
$ docker run -it -p 3000:3000 -p 3003:3003 --rm \
    acapy-kafka_queue start --arg-file default.yml --admin 0.0.0.0 3003
```

#### Configuration files

To use your own configuration files, consider loading them to a shared volume
and specifying the file on startup:

```sh
$ docker run -it -p 3000:3000 -p 3001:3001 --rm \
    -v ./configs:/local/configs:z \
    acapy-kafka_queue start --arg-file /local/configs/my_config.yml
```

#### Environment

ACA-Py will also load options from the environment. This enables using Docker
Compose `env` files to load configuration when appropriate. To see a list of
configuration options and the mapping to environment variables map, run:

```sh
$ docker run -it --rm acapy-kafka_queue start --help
```
