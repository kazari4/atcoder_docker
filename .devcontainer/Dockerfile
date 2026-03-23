FROM python:3.11.4

ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=${USER_UID}
ARG TZ=Asia/Tokyo

ENV TZ=${TZ}

# ===== 共通パッケージ & ユーザー作成 =====
RUN apt-get update && apt-get install -y \
    sudo \
    python3-pip \
    pypy3 \
    curl \
    nodejs \
    npm \
    && groupadd --gid ${USER_GID} ${USERNAME} \
    && useradd -s /bin/bash --uid ${USER_UID} --gid ${USER_GID} -m ${USERNAME} \
    && echo ${USERNAME} ALL=\(ALL\) NOPASSWD:ALL > /etc/sudoers.d/${USERNAME} \
    && chmod 0440 /etc/sudoers.d/${USERNAME} \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# ===== Python ライブラリ =====
COPY requirements.txt /tmp/

# CPython
RUN python -m pip install --upgrade pip \
    && python -m pip install -r /tmp/requirements.txt \
    && python -m pip install git+https://github.com/hinamimi/ac-library-python \
    && python -m pip install online-judge-tools

# PyPy
RUN pypy3 -m pip install --upgrade pip --break-system-packages \
    && pypy3 -m pip install -r /tmp/requirements.txt --break-system-packages \
    && pypy3 -m pip install git+https://github.com/hinamimi/ac-library-python --break-system-packages


# ===== AtCoder CLI =====
RUN npm install -g atcoder-cli

# ===== C++ ac-library =====
USER root
RUN apt-get update && apt-get install -y g++ \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
RUN git clone https://github.com/atcoder/ac-library.git /lib/ac-library
ENV CPLUS_INCLUDE_PATH=/lib/ac-library

# ===== Rust（※ USER 切り替えは維持）=====
USER vscode

RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y --default-toolchain 1.89.0

ENV PATH="/home/vscode/.cargo/bin:${PATH}"

RUN rustup default 1.89.0

RUN cargo install pahcer \
    && cargo install cargo-update \
    && cargo install-update pahcer
