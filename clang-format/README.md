
# docker-clang-format

Docker image for [clang-format](https://clang.llvm.org/docs/ClangFormat.html).

## Overview

- [Usage](#usage)
  * [With Docker](#with-docker)
  * [With Whalebrew](#with-whalebrew)
- [Build](#build)

## Usage

### With Docker

When running with Docker, you'll need to mount the current directory and change the user. Otherwise, re-formatted files will be owned by root:

```shell
docker run \
  --rm \
  -u "$(id -u):$(id -g)" \
  -v "$(pwd):$(pwd)" \
  -w "$(pwd)" \
  hdivsecurity/clang-format:latest \
  <clang-format arguments>
```

For example, tormat all `.c` and `.h` files in the current directory, recursively:

```shell
docker run \
  --rm \
  -u "$(id -u):$(id -g)" \
  -v "$(pwd):$(pwd)" \
  -w "$(pwd)" \
  hdivsecurity/clang-format:latest \
  -i --style=file $(find . -name '*.c' -o -name '*.h')
```

### With Whalebrew

This image supports [Whalebrew](https://github.com/whalebrew/whalebrew):

```
whalebrew install hdivsecurity/clang-format
clang-format -i --style=file $(find . -name '*.c' -o -name '*.h')
```

## Build

The image build process takes two optional [build arguments](https://docs.docker.com/engine/reference/commandline/build/#set-build-time-variables---build-arg):

* `PARALLEL_JOBS` (default: `4`): number of parallel jobs for the build, it should not be higher than the number of CPUs. Note that setting it to the number of CPUs may result in the build process hogging the system.
* `LLVM_TAG` (default: `llvmorg-10.0.1`): Git branch or tag from the [llvm-project](https://github.com/llvm/llvm-project) to use for the build.

To build the image for clang-format 10 you would run:

```shell
docker build --build-arg PARALLEL_JOBS=4 --build-arg LLVM_TAG=llvmorg-10.0.1 -t hdivsecurity/clang-format:10 .
```

