FROM yutouxiaozhuan/docker_images:ros2_humble

ENV DEBIAN_FRONTEND=noninteractive
ENV LANG=C.UTF-8
ENV LC_ALL=C.UTF-8

ARG USERNAME=yutou

# ======== MuJoCo 依赖 (GUI 模式 / OpenGL 渲染) ========
RUN apt-get update && apt-get install -y --no-install-recommends \
    # OpenGL 渲染
    libgl1-mesa-glx \
    libgl1-mesa-dri \
    libglfw3 \
    libglfw3-dev \
    libglew-dev \
    # X11 客户端库 (GUI 窗口渲染)
    libx11-dev \
    libx11-6 \
    libxrandr-dev \
    libxrandr2 \
    libxinerama-dev \
    libxinerama1 \
    libxcursor-dev \
    libxcursor1 \
    libxi-dev \
    libxi6 \
    libxxf86vm1 \
    libxxf86vm-dev \
    # 其他依赖
    libglib2.0-0 \
    libglib2.0-dev \
    mesa-utils \
    x11-utils \
    # 清理
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

# ======== 安装 MuJoCo 原生库（从 GitHub 下载二进制包） ========
ARG MUJOCO_VERSION=3.3.0
RUN mkdir -p /opt/mujoco && \
    cd /opt/mujoco && \
    wget -q "https://github.com/google-deepmind/mujoco/releases/download/${MUJOCO_VERSION}/mujoco-${MUJOCO_VERSION}-linux-x86_64.tar.gz" && \
    tar -xzf "mujoco-${MUJOCO_VERSION}-linux-x86_64.tar.gz" && \
    rm "mujoco-${MUJOCO_VERSION}-linux-x86_64.tar.gz" && \
    ln -s "mujoco-${MUJOCO_VERSION}" mujoco

# 设置环境变量，让系统能找到 MuJoCo 库文件和头文件
ENV MUJOCO_DIR=/opt/mujoco/mujoco
ENV LD_LIBRARY_PATH=/opt/mujoco/mujoco/lib:${LD_LIBRARY_PATH}
ENV CPATH=/opt/mujoco/mujoco/include:${CPATH}
ENV LIBRARY_PATH=/opt/mujoco/mujoco/lib:${LIBRARY_PATH}

# ======== MuJoCo 使用默认 GLFW 窗口渲染 (GUI 模式) ========
# 运行容器时需挂载 X11 socket:
#   docker run -it --rm \
#       -v /tmp/.X11-unix:/tmp/.X11-unix \
#       -e DISPLAY=$DISPLAY \
#       yutouxiaozhuan/docker_images:ros2_humble_mujoco

# ======== 恢复交互式前端 ========
ENV DEBIAN_FRONTEND=dialog

WORKDIR /home/$USERNAME/ros2_ws

CMD ["/bin/bash"]
