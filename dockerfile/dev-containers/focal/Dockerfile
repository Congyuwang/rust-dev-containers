FROM --platform=linux/amd64 ubuntu:focal

# use ustc mirror in China, comment out if you are not in China
#RUN sed -i 's@//.*archive.ubuntu.com@//mirrors.tuna.tsinghua.edu.cn@g' /etc/apt/sources.list

# remove security source to avoid connection error
#RUN sed -i '/^deb http:\/\/security/d' /etc/apt/sources.list

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get install -y -q\
    build-essential \
    software-properties-common \
    libssl-dev \
    lsb-core \
    git \
    vim \
    wget \
    curl \
    screen

# install cmake
WORKDIR /root
RUN mkdir temp
WORKDIR /root/temp
RUN curl -OL https://github.com/Kitware/CMake/releases/download/v3.27.4/cmake-3.27.4.tar.gz
RUN tar -xzvf cmake-3.27.4.tar.gz

WORKDIR /root/temp/cmake-3.27.4
RUN ./bootstrap -- -DCMAKE_BUILD_TYPE:STRING=Release
RUN make -j4
RUN make install

WORKDIR /root
RUN rm -rf temp

# install llvm@18
COPY ./update-alternatives-clang.sh /root
RUN wget https://apt.llvm.org/llvm.sh
RUN chmod +x llvm.sh
RUN ./llvm.sh 18 all
# use tsinghua mirror in China, comment out if you are not in China
#RUN ./llvm.sh 18 all -m https://mirrors.tuna.tsinghua.edu.cn/llvm-apt
RUN ./update-alternatives-clang.sh 18 9999
RUN rm llvm.sh
RUN rm update-alternatives-clang.sh

# install nightly rust
# use ustc mirror in China, comment out if you are not in China
#ARG RUSTUP_UPDATE_ROOT="https://mirrors.ustc.edu.cn/rust-static/rustup"
#ARG RUSTUP_DIST_SERVER="https://mirrors.tuna.tsinghua.edu.cn/rustup"
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y --default-toolchain 1.78.0
ENV PATH="/root/.cargo/bin:${PATH}"

RUN chsh -s /bin/bash

ENTRYPOINT ["/bin/bash"]
