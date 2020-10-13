#
# Dockerfile for clang-format
#
# It uses a multi-stage build process. The first stage clones the llvm-project
# repository and builds a statically linked clang-format.
# The second stage gets the clang-format binary into a clean base image
# so we don't have all the overhead from build tools.
#
# - LLVM is built with GCC. Building it directly with LLVM on an Alpine system
#   is a quite involved process. You don't really want to do it, but you can
#   read more about it here: https://wiki.musl-libc.org/building-llvm.html
#
# - We tried to find a set of LLVM build options that avoid building unnecessary
#   stuff. It can probably be optimized some more. See an introduction to CMake
#   options for LLVM here: https://llvm.org/docs/CMake.html#options-and-variables
#
# This process was inspired by https://github.com/angular/clang-format
#

FROM alpine:3.12 AS builder

# number of parallel build jobs, it should usually be the number of CPUs, but
# that may overload the system significantly
ARG PARALLEL_JOBS=4

# llvm-project git tag or branch to clone
ARG LLVM_TAG=llvmorg-10.0.1

ENV LLVM_REPO=https://github.com/llvm/llvm-project.git

RUN apk add --no-cache git cmake make gcc g++ binutils python3

RUN git clone --depth 1 --branch "${LLVM_TAG}" "${LLVM_REPO}"

RUN set -ex ;\
    cd llvm-project ;\
    mkdir build ;\
    cd build ;\
    cmake \
        -DCMAKE_BUILD_TYPE=MinSizeRel \
        -DLLVM_TARGETS_TO_BUILD=X86 \
        -DLLVM_BUILD_STATIC=true \
        -DLLVM_ENABLE_ZLIB=NO \
        -DLLVM_ENABLE_FFI=NO \
        -DLLVM_BUILD_DOCS=NO \
        -DLLVM_BUILD_EXAMPLES=NO \
        -DLLVM_ENABLE_PROJECTS=clang \
        -G "Unix Makefiles" \
        ../llvm ; \
    make -j ${PARALLEL_JOBS} clang-format

FROM alpine:3.12
LABEL io.whalebrew.name clang-format
LABEL io.whalebrew.config.volumes '["$PWD:$PWD"]'
LABEL io.whalebrew.config.working_dir '$PWD'
COPY --from=builder /llvm-project/build/bin/clang-format /usr/local/bin/clang-format
RUN mkdir /work
WORKDIR /work
ENTRYPOINT [ "clang-format" ]
CMD [ "-h" ]
