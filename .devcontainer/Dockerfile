FROM python:3.11.4

ARG USERNAME=vscode
ARG GROUPNAME=${USERNAME}
ARG USER_UID=1000
ARG USER_GID=${USER_UID}

RUN apt-get update \
    && groupadd --gid ${USER_GID} ${GROUPNAME} \
    && useradd -s /bin/bash --uid ${USER_UID} --gid ${USER_GID} -m ${USERNAME} \
    && apt-get -y install sudo \ 
    && echo ${USERNAME} ALL=\(ALL\) NOPASSWD:ALL > /etc/sudoers.d/${USERNAME} \
    && chmod 0440 /etc/sudoers.d/${USERNAME}

# Python3, PyPy3の環境想定
RUN apt-get update && \
    apt-get install -y python3-pip pypy3 nodejs npm

# pythonライブラリをインストール
COPY requirements.txt /tmp/
RUN python -m pip install --upgrade pip \
    && pip install -r /tmp/requirements.txt
RUN pip install git+https://github.com/hinamimi/ac-library-python

# コンテスト補助ツールをインストール
RUN pip3 install online-judge-tools
RUN npm install -g atcoder-cli


# C++環境のセットアップ（g++ インストール）
USER root
RUN apt-get update && apt-get install -y g++
# /lib/ac-library は /lib は通常 root の書き込みが可能なため、root で git clone する
RUN git clone https://github.com/atcoder/ac-library.git /lib/ac-library
# 環境変数を設定
ENV CPLUS_INCLUDE_PATH=/lib/ac-library


# Rust(AHC用)
# 必要な依存パッケージのインストール
RUN apt-get update && apt-get install -y curl build-essential

# ユーザーを vscode に切り替え
USER vscode

# vscode のホームディレクトリに rustup をインストール（非対話モード）
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y

# vscode ユーザーの PATH に rustup のバイナリディレクトリを追加
ENV PATH="/home/vscode/.cargo/bin:${PATH}"

# pahcerのインストール
RUN cargo install pahcer