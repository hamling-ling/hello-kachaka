# How to setup
#
# Build docker image
# cd docker
# docker build . --build-arg UID="`id -u`"  --build-arg GID="`id -g`" -t humble
#
# How to Run
# docker run -it --rm -v $PWD/dev_ws:/home/user/dev_ws --env="DISPLAY" --runtime=nvidia --gpus all --name humble -v /tmp/.X11-unix:/tmp/.X11-unix:rw humble bash
#
# How to Exec
# docker exec -it humble bash


FROM nvidia/cuda:12.1.0-cudnn8-devel-ubuntu22.04

ARG USERNAME=user
ARG GROUPNAME=users
ARG UID=1000
ARG GID=1000

ENV ROS_DISTRO=humble

RUN ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
RUN apt-get update && apt-get install -y \
    locales \
    curl \
    wget \
    sudo \
    gnupg2 \
    lsb-release \
    software-properties-common
RUN add-apt-repository universe

RUN localedef -f UTF-8 -i ja_JP ja_JP.UTF-8
ENV LANG="ja_JP.UTF-8" \
    LANGUAGE="ja_JP:ja" \
    LC_ALL="ja_JP.UTF-8"

RUN groupadd --gid $GID $USERNAME \
    && useradd --uid $UID --gid $GID -m $USERNAME \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | tee /etc/apt/sources.list.d/ros2.list > /dev/null
RUN wget http://packages.osrfoundation.org/gazebo.key -O - | sudo apt-key add -

RUN apt-get update && apt-get install -y \
    python3-flake8-docstrings \
    python3-pip \
    python3-pytest-cov \
    ros-dev-tools \
    python3-flake8-blind-except \
    python3-flake8-builtins \
    python3-flake8-class-newline \
    python3-flake8-comprehensions \
    python3-flake8-deprecated \
    python3-flake8-import-order \
    python3-flake8-quotes \
    python3-pytest-repeat \
    python3-pytest-rerunfailures \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y \
    ros-humble-desktop \
    python3-colcon-common-extensions \
    ignition-fortress \
    ros-humble-ros-gz-bridge \
    ros-humble-slam-toolbox \
    ros-humble-navigation2 \
    ros-humble-ros-ign \
    ros-humble-joint-state-publisher \
    ros-humble-joint-state-publisher-gui \
    x11-apps \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN bash -c "rosdep init && rosdep update"

COPY entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

WORKDIR /home/$USERNAME/dev_ws
USER $USERNAME
RUN echo "source /opt/ros/${ROS_DISTRO}/setup.bash" >> ~/.bashrc
RUN . "/opt/ros/${ROS_DISTRO}/setup.sh"
RUN rosdep update

CMD ["bash"]
