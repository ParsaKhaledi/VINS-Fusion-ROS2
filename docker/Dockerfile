FROM ros:foxy-ros-base

SHELL ["/bin/bash", "-c"]

ENV DEBIAN_FRONTEND=noninteractive
ENV ROS_DISTRO=foxy
ENV CERES_VERSION="2.1.0"
ENV WS=/ws_vins-fusion-ros2
ENV TERM xterm
ENV PYTHONIOENCODING UTF-8

WORKDIR $WS

      # set up thread number for building
RUN   apt update && apt install -y --no-install-recommends \
      cmake git wget unzip python3-pip libboost-all-dev libopencv-dev \ 
      cmake-data build-essential libgflags-dev libgoogle-glog-dev \
      protobuf-compiler libprotobuf-dev \
      ros-${ROS_DISTRO}-cv-bridge       ros-${ROS_DISTRO}-image-transport \
      ros-${ROS_DISTRO}-message-filters ros-${ROS_DISTRO}-tf* \
      ros-${ROS_DISTRO}-rqt*            ros-${ROS_DISTRO}-rviz* \
	ros-${ROS_DISTRO}-cv-bridge       ros-${ROS_DISTRO}-tf2 \
      ros-${ROS_DISTRO}-tf2-ros         ros-${ROS_DISTRO}-image-transport     &&\
      pip3 install numpy opencv-python==3.4.18.65 colcon-ros pytest==5.3
      # libatlas-base-dev \
      # libeigen3-dev \
      # libgoogle-glog-dev \
      # libsuitesparse-dev \
      # libglew-dev \
      # libtiff5-dev \
      # zlib1g-dev \
      # libjpeg-dev \
      # libpng-dev \
      # libavcodec-dev \
      # libavformat-dev \
      # libavutil-dev \
      # libpostproc-dev \
      # libswscale-dev \
      # libeigen3-dev \
      # libtbb-dev \
      # libgtk2.0-dev \
      # pkg-config python3-distutils
      
#Install Eigen
RUN	cd ${WS} &&\
      wget -O eigen-3.3.9.zip https://gitlab.com/libeigen/eigen/-/archive/3.3.9/eigen-3.3.9.zip  &&\
	unzip eigen-3.3.9.zip &&\
	cd eigen-3.3.9 && mkdir build && cd build &&\
	cmake ../ && sudo make install -j $(nproc) &&\
	cd ${WS} && rm -rf eigen-3.3.9.zip eigen-3.3.9

# Install Ceres
RUN   cd ${WS} &&\
      wget http://ceres-solver.org/ceres-solver-2.1.0.tar.gz &&\
      tar zxf ceres-solver-2.1.0.tar.gz &&\
      cd ceres-solver-2.1.0 &&\
      mkdir build && cd build &&\
      cmake -DEXPORT_BUILD_DIR=ON \
      -DCMAKE_INSTALL_PREFIX=/usr/local \
      ../ && make -j$(nproc) && make install -j $(nproc) &&\	 
      cd ../ && rm -rf ceres-solver-2.1.0.tar.gz ceres-solver-2.1.0

# RUN   mkdir -p /opencv_dir && cd opencv_dir && \
#       git clone git clone https://github.com/opencv/opencv.git -b v3.4.1 && \
#       git clone https://github.com/opencv/opencv_extra.git -b v3.4.1 && \
#       cd ./opencv && mkdir build && cd build && cmake \
#       -DCMAKE_BUILD_TYPE=Release \
#       -DCMAKE_INSTALL_PREFIX=/usr \
#       -DBUILD_opencv_python3=ON \
#       -DWITH_OPENCL=OFF \
#       -DWITH_OPENMP=OFF \
#       -DWITH_FFMPEG=ON \
#       -DWITH_GSTREAMER=OFF \
#       -DWITH_CUDA=ON \
#       -DWITH_GTK=ON \
#       -DWITH_VTK=OFF \
#       -DWITH_TBB=ON \
#       -DWITH_1394=OFF \
#       -DWITH_OPENEXR=OFF \
#       -DCUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda-8.0 \
#       -DOPENCV_TEST_DATA_PATH=../opencv_extra/testdata \
#       ../opencv && make -j4 VERBOSE=1 && make test && make install 

# Copy VINS-Fusion
RUN   mkdir -p ${WS}/src/VINS-Fusion-ROS2
COPY  ../ ${WS}/src/VINS-Fusion-ROS2

# Build VINS-Fusion
RUN   cd ${WS} && source /opt/ros/${ROS_DISTRO}/setup.bash && colcon build --packages-skip loop_fusion

RUN   echo "${WS}/install/setup.bash" >> ~/.bashrc &&\
      rm -rf /var/lib/apt/lists/*

