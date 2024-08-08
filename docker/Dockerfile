FROM ros:humble-ros-base

SHELL ["/bin/bash", "-c"]

ENV DEBIAN_FRONTEND=noninteractive
ENV ROS_DISTRO=humble
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
      ros-${ROS_DISTRO}-tf2-ros         ros-${ROS_DISTRO}-imu-tools     &&\
      pip3 install numpy colcon-ros pytest==5.3 #opencv-python==3.4.18.65
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
RUN   wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/arm64/cuda-keyring_1.1-1_all.deb &&\
      dpkg -i cuda-keyring_1.1-1_all.deb && apt update && \
      apt-get -y install --no-install-recommends cuda-toolkit-12-5 
      
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

RUN   mkdir -p /opencv_dir && cd /opencv_dir && \
      git clone git clone https://github.com/opencv/opencv.git -b v3.4.1 && \
      git clone https://github.com/opencv/opencv_extra.git -b v3.4.1 && \
      git clone git clone https://github.com/opencv/opencv_contrib.git -b v3.4.1 &&\
      cd ./opencv && mkdir build && cd build && cmake \
      -D CMAKE_BUILD_TYPE=Release \
      -D CMAKE_INSTALL_PREFIX=/usr/local \
      -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules \
      -D BUILD_opencv_python3=ON \
      -D WITH_OPENCL=OFF \
      -D WITH_OPENMP=OFF \
      -D WITH_FFMPEG=ON \
      -D WITH_GSTREAMER=OFF \
      -D WITH_CUDA=ON \
      -D CUDA_FAST_MATH=1 \
      -D ENABLE_FAST_MATH=1 \ 
      -D WITH_CUBLAS=1 \
      -D WITH_GTK=ON \
      -D WITH_VTK=OFF \
      -D WITH_TBB=ON \
      -D WITH_1394=OFF \
      -D WITH_OPENEXR=OFF \
      -D CUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda-8.0 \
      -D OPENCV_TEST_DATA_PATH=../opencv_extra/testdata \
      ../opencv
RUN   cd /opencv_dir/opencv/build && make -j4 VERBOSE=1 && make test && make install && \
      cd ../ rm -rf ./build/

# Copy VINS-Fusion
# RUN   mkdir -p ${WS}/src/VINS-Fusion-ROS2
# COPY  ../ ${WS}/src/VINS-Fusion-ROS2
RUN   mkdir -p ${WS}/src/ && cd ${WS}/src && \
      git clone https://github.com/bonabai/VINS-Fusion-ROS2.git -b no_cuda
      # git clone https://github.com/zinuok/VINS-Fusion-ROS2.git && cd VINS-Fusion-ROS2 && \
      # git checkout b02d4154e3d72fcd674f62a6347770cfc546fe48

# Build VINS-Fusion
# RUN   cd ${WS} && source /opt/ros/${ROS_DISTRO}/setup.bash && colcon build --packages-skip loop_fusion
RUN   cd ${WS} && source /opt/ros/${ROS_DISTRO}/setup.bash && colcon build 

RUN   echo "${WS}/install/setup.bash" >> ~/.bashrc &&\
      rm -rf /var/lib/apt/lists/*



# FROM ros:humble-ros-base

# SHELL ["/bin/bash", "-c"]

# ENV DEBIAN_FRONTEND=noninteractive
# ENV ROS_DISTRO=humble
# ENV CERES_VERSION="2.1.0"
# ENV WS=/ws_vins-fusion-ros2
# ENV TERM xterm
# ENV PYTHONIOENCODING UTF-8

# WORKDIR $WS

#       # set up thread number for building
# RUN   apt update && apt install -y --no-install-recommends \
#       cmake git wget unzip python3-pip libboost-all-dev \ 
#       build-essential \
#       ros-${ROS_DISTRO}-cv-bridge       ros-${ROS_DISTRO}-image-transport \
#       ros-${ROS_DISTRO}-message-filters ros-${ROS_DISTRO}-tf* \
#       ros-${ROS_DISTRO}-rqt*            ros-${ROS_DISTRO}-rviz* \
#       ros-${ROS_DISTRO}-cv-bridge       ros-${ROS_DISTRO}-tf2 \
#       ros-${ROS_DISTRO}-tf2-ros         ros-${ROS_DISTRO}-imu-tools     &&\
#       pip3 install numpy colcon-ros pytest==5.3
#       # libatlas-base-dev \
#       # libeigen3-dev \
#       # libgoogle-glog-dev \
#       # libsuitesparse-dev \
#       # libglew-dev \
#       # libtiff5-dev \
#       # zlib1g-dev \
#       # libjpeg-dev \
#       # libpng-dev \
#       # libavcodec-dev \
#       # libavformat-dev \
#       # libavutil-dev \
#       # libpostproc-dev \
#       # libswscale-dev \
#       # libeigen3-dev \
#       # libtbb-dev \
#       # libgtk2.0-dev \
#       # pkg-config python3-distutils
# RUN   wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/arm64/cuda-keyring_1.1-1_all.deb &&\
#       dpkg -i cuda-keyring_1.1-1_all.deb && apt update && \
#       apt-get -y install --no-install-recommends cuda-toolkit-12-5 cuda-compat-12-5
      
# #Install Eigen
# RUN	cd ${WS} &&\
#       wget -O eigen-3.3.9.zip https://gitlab.com/libeigen/eigen/-/archive/3.3.9/eigen-3.3.9.zip  &&\
# 	unzip eigen-3.3.9.zip &&\
# 	cd eigen-3.3.9 && mkdir build && cd build &&\
# 	cmake ../ && sudo make install -j $(nproc) &&\
# 	cd ${WS} && rm -rf eigen-3.3.9.zip eigen-3.3.9

# # Install Ceres
# RUN   cd ${WS} &&\
#       wget http://ceres-solver.org/ceres-solver-2.1.0.tar.gz &&\
#       tar zxf ceres-solver-2.1.0.tar.gz &&\
#       cd ceres-solver-2.1.0 &&\
#       mkdir build && cd build &&\
#       cmake -DEXPORT_BUILD_DIR=ON \
#       -DCMAKE_INSTALL_PREFIX=/usr/local \
#       ../ && make -j$(nproc) && make install -j $(nproc) &&\	 
#       cd ../ && rm -rf ceres-solver-2.1.0.tar.gz ceres-solver-2.1.0

# RUN   mkdir -p /opencv_dir && cd opencv_dir && \
#       git clone git clone https://github.com/opencv/opencv.git -b v3.4.1 && \
#       git clone https://github.com/opencv/opencv_extra.git -b v3.4.1 && \
#       git clone git clone https://github.com/opencv/opencv_contrib.git -b v3.4.1 &&\
#       cd ./opencv && mkdir build && cd build && cmake \
#       -D CMAKE_BUILD_TYPE=Release \
#       -D CMAKE_INSTALL_PREFIX=/usr/local \
#       -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules \
#       -D BUILD_opencv_python3=ON \
#       -D WITH_OPENCL=OFF \
#       -D WITH_OPENMP=OFF \
#       -D WITH_FFMPEG=ON \
#       -D WITH_GSTREAMER=OFF \
#       -D WITH_CUDA=ON \
#       -D CUDA_FAST_MATH=1 \
#       -D ENABLE_FAST_MATH=1 \ 
#       -D WITH_CUBLAS=1 \
#       -D WITH_GTK=ON \
#       -D WITH_VTK=OFF \
#       -D WITH_TBB=ON \
#       -D WITH_1394=OFF \
#       -D WITH_OPENEXR=OFF \
#       -D CUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda-8.0 \
#       -D OPENCV_TEST_DATA_PATH=../opencv_extra/testdata \
#       ../opencv && make -j4 VERBOSE=1 && make test && make install && \
#       cd ../ rm -rf ./build/

# # Copy VINS-Fusion
# # RUN   mkdir -p ${WS}/src/VINS-Fusion-ROS2
# # COPY  ../ ${WS}/src/VINS-Fusion-ROS2
# RUN   mkdir -p ${WS}/src/ && cd ${WS}/src && \
#       git clone https://github.com/bonabai/VINS-Fusion-ROS2.git # -b no_cuda
#       # git clone https://github.com/zinuok/VINS-Fusion-ROS2.git && cd VINS-Fusion-ROS2 && \
#       # git checkout b02d4154e3d72fcd674f62a6347770cfc546fe48

# # Build VINS-Fusion
# # RUN   cd ${WS} && source /opt/ros/${ROS_DISTRO}/setup.bash && colcon build --packages-skip loop_fusion
# RUN   cd ${WS} && source /opt/ros/${ROS_DISTRO}/setup.bash && colcon build 

# RUN   echo "${WS}/install/setup.bash" >> ~/.bashrc &&\
#       rm -rf /var/lib/apt/lists/*

