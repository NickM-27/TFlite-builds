FROM debian:12 AS build
ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get -qq update \
    && apt-get -qq install -y \
    apt-transport-https \
    gnupg \
    wget \
    && apt-get -qq update \
    && apt-get -qq install -y \
    build-essential cmake git \
    software-properties-common \
    && rm -rf /var/lib/apt/lists/*

# pull repo for build
RUN git clone https://github.com/tensorflow/tensorflow.git && \
    cd tensorflow && \
    git checkout v2.17.0

# modifications for debian and py311 build
RUN sed -i '52a\RUN rm /usr/lib/python\$PYTHON_VERSION/EXTERNALLY-MANAGED' tensorflow/tensorflow/lite/tools/pip_package/Dockerfile.py3 && \
    sed -i '/RUN yes | add-apt-repository ppa:deadsnakes\/ppa/d' tensorflow/tensorflow/lite/tools/pip_package/Dockerfile.py3

ENV BASE_IMAGE debian:12
ENV PYTHON_VERSION 3.11
ENV NUMPY_VERSION 1.24.4

# run the build
RUN BUILD_NUM_JOBS=4 make -C tensorflow/tensorflow/lite/tools/pip_package docker-build TENSORFLOW_TARGET=default PYTHON_VERSION=3.11

FROM scratch AS package
COPY --from=build / /
