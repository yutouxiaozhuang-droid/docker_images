FROM osrf/ros:humble-desktop-full

# 设置非交互式安装，避免 tzdata 等包卡住
ENV DEBIAN_FRONTEND=noninteractive
ENV LANG=C.UTF-8
ENV LC_ALL=C.UTF-8

# 更新 apt 源并安装日常开发工具
RUN apt-get update && apt-get upgrade -y && apt-get install -y --no-install-recommends \
    # ======== 基础构建工具 ========
    build-essential \
    cmake \
    gdb \
    pkg-config \
    autoconf \
    automake \
    libtool \
    make \
    ninja-build \
    ccache \
    # ======== 版本控制 ========
    git \
    git-lfs \
    # ======== 编辑器 ========
    vim \
    nano \
    # ======== 网络工具 ========
    curl \
    wget \
    net-tools \
    iputils-ping \
    ca-certificates \
    openssh-client \
    # ======== Python 开发 ========
    python3-pip \
    python3-venv \
    python3-dev \
    python3-setuptools \
    # ======== ROS2 开发工具 ========
    python3-colcon-common-extensions \
    python3-vcstool \
    python3-rosinstall-generator \
    # ======== 终端 / Shell 工具 ========
    htop \
    tmux \
    screen \
    bash-completion \
    fish \
    # ======== 搜索 & 文件工具 ========
    ripgrep \
    fd-find \
    tree \
    unzip \
    zip \
    jq \
    less \
    # ======== 系统工具 ========
    sudo \
    locales \
    lsb-release \
    gnupg2 \
    software-properties-common \
    xdg-utils \
    ros-humble-foxglove-bridge \
    nautilus \
    # ======== 清理 apt 缓存 ========
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

# ======== 安装 Nushell ========
# 方式1: 通过 cargo 安装 (功能完整但较慢) 或 方式2: 下载预编译二进制
# 这里使用下载预编译二进制的方式，更快更稳定
RUN ARCH=$(uname -m) \
    && case "$ARCH" in \
        x86_64)  NU_ARCH="x86_64-unknown-linux-gnu" ;; \
        aarch64) NU_ARCH="aarch64-unknown-linux-gnu" ;; \
        *)       echo "Unsupported architecture: $ARCH" && exit 1 ;; \
    esac \
    && curl -fsSL "https://github.com/nushell/nushell/releases/download/0.103.0/nu-0.103.0-${NU_ARCH}.tar.gz" \
        -o /tmp/nu.tar.gz \
    && tar -xzf /tmp/nu.tar.gz -C /usr/local/bin/ \
    && rm -f /tmp/nu.tar.gz \
    && chmod +x /usr/local/bin/nu-*

# ======== 配置 Nushell 环境 ========
# 创建 nushell 配置目录
RUN mkdir -p /root/.config/nushell

# 将 nu 添加到 /etc/shells 以便作为登录 shell
RUN echo "/usr/local/bin/nu" >> /etc/shells

# ======== 创建普通用户 (可选，方便日常开发) ========
ARG USERNAME=yutou
ARG USER_UID=1000
ARG USER_GID=1000
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && echo "$USERNAME ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers \
    && usermod -aG sudo $USERNAME \
    && usermod -aG video $USERNAME \
    && usermod -aG audio $USERNAME

# 给普通用户也创建 nushell 配置目录
RUN mkdir -p /home/$USERNAME/.config/nushell \
    && chown -R $USERNAME:$USERNAME /home/$USERNAME/.config

# 恢复交互式前端
ENV DEBIAN_FRONTEND=dialog

# 默认使用 bash，如需 nu 请在 docker run 时指定: docker run -it --rm xxx nu
CMD ["/bin/bash"]
