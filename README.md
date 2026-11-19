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

## flashing with nrfutil + J-Link

`Dockerfile.flash` is a slim standalone image (no Zephyr build toolchain) with
`nrfutil` and the SEGGER J-Link tools, for flashing prebuilt firmware over a
J-Link. It is architecture-aware (`TARGETARCH`): it pulls the matching nrfutil
and J-Link builds for `amd64` and `arm64`.

On `arm64` the base must stay Debian 12 (the image uses `python:3.12-slim-bookworm`,
which is bookworm), since that is the only Linux ARM64 target Nordic supports for
nrfutil. The `nrf5sdk-tools` component is not available on ARM64, so it is only
installed on `amd64` and skipped elsewhere; the `nrfutil device` flashing command
works on both.

```bash
# build
docker build -f Dockerfile.flash -t zephyr-flash .

# flash a prebuilt hex (Linux host, J-Link plugged in)
docker run --rm -it \
  --privileged -v /dev/bus/usb:/dev/bus/usb \
  -v "$PWD:/work" -w /work \
  zephyr-flash \
  nrfutil device program --firmware build/zephyr/zephyr.hex --traits jlink
```

Device access is granted by USB passthrough (`--privileged -v /dev/bus/usb:/dev/bus/usb`,
or the tighter `--device=/dev/bus/usb/<bus>/<dev>`); the container's udev rules do
not run since there is no udevd inside it.

## resulting images:
```bash
~ > docker images
IMAGE                            ID             DISK USAGE   CONTENT SIZE   EXTRA
zephyr-arm-slim:latest           042a8991985a       3.42GB             0B
zephyr-ci:latest                 51d9b4a47020       13.9GB             0B
zephyr-devel:latest              58e491b25e58       13.7GB             0B
zephyr-full-sdk:latest           b21e0a1ab05d       12.3GB             0B
``` 
