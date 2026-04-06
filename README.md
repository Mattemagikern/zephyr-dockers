# Docker workflow example

## dependencies

- Docker
- act 

## run

```bash
# run sdk-build
act -j build-sdk
```

```bash
# run sdk-build
act -j build-devel
```

## fake ping from github action
```bash
atc repository_dispatch -e [devel-event.json | sdk-event.json]
``` 

## resulting images:
```bash
~ > docker images
IMAGE                            ID             DISK USAGE   CONTENT SIZE   EXTRA
zephyr-arm-slim:latest           042a8991985a       3.42GB             0B
zephyr-ci:latest                 51d9b4a47020       13.9GB             0B
zephyr-devel:latest              58e491b25e58       13.7GB             0B
zephyr-full-sdk:latest           b21e0a1ab05d       12.3GB             0B
``` 
