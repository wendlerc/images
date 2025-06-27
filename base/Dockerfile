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

# Install Miniforge with better error handling
RUN wget -O /tmp/Miniforge3.sh "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh" && \
    bash /tmp/Miniforge3.sh -b -p "/opt/conda" && \
    rm /tmp/Miniforge3.sh

# Clean conda cache and temporary files (separate step for easier debugging)
RUN /opt/conda/bin/conda clean -afy || true && \
    find /opt/conda -name "*.pyc" -type f -delete 2>/dev/null || true && \
    find /opt/conda -name "*.pyo" -type f -delete 2>/dev/null || true && \
    find /opt/conda -name "*~" -type f -delete 2>/dev/null || true

# Source conda in the same layer where it's needed
RUN . /opt/conda/etc/profile.d/conda.sh

# Make conda install writable
RUN chmod -R 777 /opt/conda

# Setup SSH
RUN ssh-keygen -A

EXPOSE 22

COPY base/.bashrc /tmp/.bashrc

CMD ["/bin/bash"]
