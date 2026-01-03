
FROM debian:12 as base
ENV DEBIAN_FRONTEND=noninteractive
# tflite dependencies, see https://github.com/tensorflow/tensorflow/tree/master/tensorflow/lite/tools/pip_package
RUN apt-get -qq update \
    && apt-get -qq install -y \
    python3 \
    python3-dev \
    python3-pip \
    python3-numpy \
    python3-pybind11 \
    swig libjpeg-dev zlib1g-dev git 

FROM base as build_tflite
RUN git clone https://github.com/tensorflow/tensorflow.git
RUN cd tensorflow && git checkout v2.20.0
RUN bash tensorflow/tensorflow/lite/tools/make/download_dependencies.sh
RUN env BUILD_NUM_JOBS=$(($(grep -c "^processor" /proc/cpuinfo) / 2)) bash tensorflow/tensorflow/lite/tools/pip_package/build_pip_package_with_cmake.sh
RUN ls -ltra /tensorflow/tensorflow/lite/tools/pip_package/gen/tflite_pip/python3/dist

FROM ghcr.io/blakeblackshear/frigate:0.17.0-beta1
COPY --from=build_tflite /tensorflow/tensorflow/lite/tools/pip_package/gen/tflite_pip/python3/dist/tflite_runtime-2.20.0-cp39-cp39-linux_x86_64.whl /wheels/
RUN pip3 install --upgrade /wheels/tflite_runtime-2.20.0-cp39-cp39-linux_x86_64.whl
