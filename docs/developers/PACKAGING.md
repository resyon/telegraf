# Packaging

Building the packages for Telegraf is automated using [Make](https://en.wikipedia.org/wiki/Make_(software)). Just running `make` will build a Telegraf binary for the operating system and architecture you are using (if it is supported). If you need to build a different package then you can run `make package` which will build all the supported packages. You will most likely only want a subset, you can define a subset of packages to be built by overriding the `include_packages` variable like so `make package include_packages="amd64.deb"`. You can also build all packages for a specific architecture like so `make package include_packages="$(make amd64)"`.

The packaging steps require certain tools to be setup before hand to work. These dependencies are listed in the ci-1.16.docker file which you can find in the scripts directory. Therefore it is recommended to use Docker to build the artifacts, see more details below.

## Package using Docker

This packaging method uses the CI images, and is very similar to how the
official packages are created on release.  This is the recommended method for
building the rpm/deb as it is less system dependent.

Pull the CI images from quay, the version corresponds to the version of Go
that is used to build the binary:
```
docker pull quay.io/influxdb/telegraf-ci:1.9.7
```

Start a shell in the container:
```
docker run -ti quay.io/influxdb/telegraf-ci:1.9.7 /bin/bash
```

From within the container:

1. `go get -d github.com/influxdata/telegraf`
2. `cd /go/src/github.com/influxdata/telegraf`
3. `git checkout release-1.10`
   * Replace tag `release-1.10` with the version of Telegraf you would like to build
4. `git reset --hard 1.10.2`
5. `make deps`
6. `make package include_packages="amd64.deb"`
    * Change `include_packages` to change what package you want, run `make help` to see possible values

From the host system, copy the build artifacts out of the container:
```
docker cp romantic_ptolemy:/go/src/github.com/influxdata/telegraf/build/telegraf-1.10.2-1.x86_64.rpm .
```
