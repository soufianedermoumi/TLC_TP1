FROM ubuntu:16.04 AS ubuntu

ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64

RUN apt update && \
# install required tools
    apt install -y git unzip ant build-essential \
                   cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev \
                   python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libdc1394-22-dev \
                   python3 python3-dev python3-numpy \
                   software-properties-common debconf-utils && \
# install openjdk-8
    apt install -y openjdk-8-jdk && \
# libjasper-dev
    curl -fs http://security.ubuntu.com/ubuntu/pool/main/j/jasper/libjasper1_1.900.1-debian1-2.4ubuntu1.2_amd64.deb -o /tmp/libjasper1.deb && \
    curl -fs http://security.ubuntu.com/ubuntu/pool/main/j/jasper/libjasper-dev_1.900.1-debian1-2.4ubuntu1.2_amd64.deb -o /tmp/libjasper-dev.deb && \
    apt install /tmp/libjasper1.deb /tmp/libjasper-dev.deb && \
    rm -rf /tmp/* && \
# download and prepare opencv
    curl -fsL https://github.com/opencv/opencv/archive/3.4.10.zip -o /tmp/opencv.zip && \
    cd /tmp && \
    unzip opencv.zip && \
    mv opencv-* opencv && \
    cd opencv && \
    mkdir build && \
# build opencv
    cd /tmp/opencv/build && \
    cmake \
    -D CMAKE_BUILD_TYPE=Release \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D WITH_FFMPEG=OFF \
    -D WITH_IPP=OFF \
    -D WITH_OPENEXR=OFF \
    -D BUILD_EXAMPLES=OFF \
    -D BUILD_ANDROID_EXAMPLES=OFF \
    -D INSTALL_PYTHON_EXAMPLES=OFF \
    -D BUILD_DOCS=OFF \
    -D BUILD_opencv_python2=OFF \
    -D BUILD_opencv_python3=OFF \
    -D BUILD_SHARED_LIBS=OFF \
    -D BUILD_TESTS=OFF \
    -D BUILD_PERF_TESTS=OFF \
    .. && \
    make -j8
#RUN cd /tmp/opencv/build && make install
