FROM ubuntu:22.04

ARG ARCH_NAME

ENV ARCH_NAME=${ARCH_NAME}
ARG DEBIAN_FRONTEND=noninteractive

# Install build and runtime deps
RUN apt-get update && apt-get install -y \
    software-properties-common \
    build-essential \
    cmake \
    ninja-build \
    git \
    git-lfs \
    libhwloc-dev \
    libgtest-dev \
    libyaml-cpp-dev \
    libboost-all-dev \
    wget \
    yamllint \
    python3-dev \
    python3-pip


RUN apt-get install -y gcc-12 g++-12

# Install clang 17
RUN wget https://apt.llvm.org/llvm.sh && \
    chmod u+x llvm.sh && \
    ./llvm.sh 17 && \
    apt-get install -y libc++-17-dev libc++abi-17-dev && \
    ln -s /usr/bin/clang-17 /usr/bin/clang && \
    ln -s /usr/bin/clang++-17 /usr/bin/clang++

# Install clang-format
RUN apt-get install -y clang-format-17 && \
    ln -s /usr/bin/clang-format-17 /usr/bin/clang-format

RUN rm -rf /var/lib/apt/lists/*

RUN git clone --recurse-submodules https://github.com/tenstorrent/tt-umd /usr/tt-umd

WORKDIR /usr/tt-umd

RUN cmake -B ./build -G Ninja \
            -DTT_UMD_BUILD_TESTS=ON \
            -DTT_UMD_BUILD_SIMULATION=ON \
            -DTT_UMD_BUILD_PYTHON=ON \
            -DCMAKE_BUILD_TYPE=Release && \ 
          cmake --build ./build --target umd_tests ubench nanobind_libdevice

WORKDIR /usr/tt-umd
ENTRYPOINT ["bash", "-c", "./build/test/umd/api/api_tests && ./build/test/umd/${ARCH_NAME}/unit_tests && ./build/test/umd/test_pcie_device/test_pcie_device && ./build/test/umd/misc/umd_misc_tests && ./build/test/umd/unified/unified_tests"]
