FROM debian:12 as base
ENV DEBIAN_FRONTEND=noninteractive
# tflite dependencies, see https://github.com/tensorflow/tensorflow/tree/master/tensorflow/lite/tools/pip_package
RUN apt-get -qq update && \
    apt-get -qq install -y \
    python3.11 \
    python3-dev \
    python3-numpy \
    python3-setuptools \
    python3-pybind11 \
    build-essential cmake git pkg-config \
    swig libjpeg-dev zlib1g-dev

FROM base as build_tflite
RUN git clone --depth 1 --branch v2.17.1 https://github.com/tensorflow/tensorflow.git && \
    env BUILD_NUM_JOBS=$(($(grep -c "^processor" /proc/cpuinfo) / 2)) bash tensorflow/tensorflow/lite/tools/pip_package/build_pip_package_with_cmake.sh

FROM ghcr.io/blakeblackshear/frigate:0.17.0-beta1
COPY --from=build_tflite /tensorflow/tensorflow/lite/tools/pip_package/gen/tflite_pip/python3/dist/tflite_runtime-2.17.1-cp311-cp311-linux_x86_64.whl /wheels/
RUN pip3 install --upgrade /wheels/tflite_runtime-2.17.1-cp311-cp311-linux_x86_64.whl
