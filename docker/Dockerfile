FROM ros:humble-ros-base

ENV DEBIAN_FRONTEND=noninteractive
ENV ROS_DISTRO=humble
ENV CERES_VERSION="2.1.0"
ENV CATKIN_WS=/catkin_ws
ENV TERM xterm
ENV PYTHONIOENCODING UTF-8

WORKDIR $CATKIN_WS

      # set up thread number for building
RUN   apt update && apt install -y --no-install-recommends \
      cmake git wget unzip nvidia-cuda-toolkit \ 
      python3-dev python3-numpy python3-py python3-pytest\
      cmake-data build-essential \
      ros-${ROS_DISTRO}-cv-bridge ros-${ROS_DISTRO}-image-transport\
      ros-${ROS_DISTRO}-message-filters ros-${ROS_DISTRO}-tf* \
      ros-${ROS_DISTRO}-rqt* ros-${ROS_DISTRO}-rviz* 
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
      # pkg-config
      
      # Build and install Ceres
RUN   git clone https://ceres-solver.googlesource.com/ceres-solver && \
      cd ceres-solver && \
      git checkout tags/${CERES_VERSION} && \
      mkdir build && cd build && \
      cmake .. && \
      make -j$(USE_PROC) install && \
      rm -rf ../../ceres-solver 

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
COPY ./ $CATKIN_WS/src/VINS-Fusion/
# use the following line if you only have this dockerfile

# Build VINS-Fusion
RUN   cd $CATKIN_WS && source /opt/ros/${ROS_DISTRO}/setup.bash && colcon build 

RUN   echo "$WORKDIR/install/setup.bash" >> ~/.bashrc &&\
      rm -rf /var/lib/apt/lists/*

