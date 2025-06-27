ARG BASE_IMAGE="pytorch/pytorch:2.7.0-cuda12.8-cudnn9-devel"
FROM ${BASE_IMAGE}

USER root

# Combine RUN commands and clean up in the same layer to reduce image size
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    curl \
    gcc \
    git \
    gosu \
    git-lfs \
    htop \
    libgl1 \
    libglib2.0-0 \
    ncdu \
    tree \
    openssh-client \
    openssh-server \
    psmisc \
    rsync \
    screen \
    sudo \
    tmux \
    unzip \
    vim \
    ldap-utils \
    nvtop \
    wget && \
    wget -q https://github.com/justjanne/powerline-go/releases/download/v1.24/powerline-go-linux-"$(dpkg --print-architecture)" -O /usr/local/bin/powerline-shell && \
    chmod a+x /usr/local/bin/powerline-shell && \
    # Clean up apt cache to reduce layer size
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Update and configure existing conda
RUN /opt/conda/bin/conda update -n base -c defaults conda && \
    /opt/conda/bin/conda clean -afy

# Source conda in the same layer where it's needed
RUN . /opt/conda/etc/profile.d/conda.sh

# Make conda install writable
RUN chmod -R 777 /opt/conda

# Setup SSH
RUN ssh-keygen -A

EXPOSE 22

# Create a non-root user
ARG USERNAME=developer
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN groupadd --gid $USER_GID $USERNAME && \
    useradd --uid $USER_UID --gid $USER_GID -m -s /bin/bash $USERNAME && \
    echo "$USERNAME ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Copy .bashrc to the user's home directory
COPY base/.bashrc /home/$USERNAME/.bashrc
RUN chown $USERNAME:$USERNAME /home/$USERNAME/.bashrc

# Make sure the user can use conda
RUN echo '. /opt/conda/etc/profile.d/conda.sh' >> /home/$USERNAME/.bashrc && \
    echo 'conda activate base' >> /home/$USERNAME/.bashrc

# Switch to the non-root user
USER $USERNAME
WORKDIR /home/$USERNAME

CMD ["/bin/bash"]
