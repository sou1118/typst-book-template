FROM rust:latest AS builder

ARG USERNAME=dev
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m -s /bin/bash $USERNAME

RUN apt-get update \
    && apt-get -y install --no-install-recommends \
    git vim curl ca-certificates sudo build-essential \
    && apt-get autoremove -y \
    && apt-get clean -y

USER $USERNAME

RUN cargo install --git https://github.com/typst/typst --locked typst-cli

FROM ubuntu:22.04

ARG USERNAME=dev
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m -s /bin/bash $USERNAME \
    && apt-get update \
    && apt-get install -y sudo \
    && echo "$USERNAME ALL=(root) NOPASSWD:ALL" > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME \
    && apt-get clean

RUN apt-get update \
    && apt-get install -y \
    texlive texlive-lang-japanese texlive-fonts-recommended fonts-noto-cjk texlive-luatex \
    && apt-get autoremove -y \
    && apt-get clean -y

RUN apt install -y locales
RUN sed -i '/en_US.UTF-8/s/^# //g' /etc/locale.gen \
    && locale-gen

USER $USERNAME

COPY --from=builder /usr/local/cargo/bin/typst /usr/local/bin/typst

CMD ["/bin/bash"]
