FROM archlinux:latest

RUN pacman -Sy --noconfirm archlinux-keyring && \
    pacman -Syu --noconfirm && \
    pacman -S --noconfirm base-devel git sudo poke

ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=1000
RUN groupadd -g $USER_GID $USERNAME && \
    useradd -m -u $USER_UID -g $USER_GID $USERNAME && \
    echo "$USERNAME ALL=(ALL) NOPASSWD:ALL" >/etc/sudoers.d/90-$USERNAME && \
    chmod 0440 /etc/sudoers.d/90-$USERNAME

USER $USERNAME
WORKDIR /workspaces