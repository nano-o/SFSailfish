FROM ubuntu:22.04

RUN apt-get update && apt-get install -y --no-install-recommends \
    curl ca-certificates \
    openjdk-21-jre-headless \
    make sudo wget \
    texlive \
    vim-tiny nano emacs-nox \
    git \
    python3.10 python3.10-dev python3-pip python3.10-venv \
    tmux \
    clang build-essential \
    ssh

RUN useradd -m -u 1000 -s /bin/bash user && \
    echo "user ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/user && \
    chmod 0440 /etc/sudoers.d/user

WORKDIR /home/user/
USER user

RUN curl -sSf https://sh.rustup.rs | sh -s -- -y --profile minimal --no-modify-path
ENV PATH="/home/user/.cargo/bin:${PATH}"

RUN git clone https://github.com/nano-o/SFSailfish /home/user/CCS2025-artifact
WORKDIR /home/user/CCS2025-artifact
RUN git fetch --all
RUN git checkout overview
# For ease of navigation, checkout each experiment branch into its own directory:
RUN for branch in OptSFSailfish SFBullshark SFBullsharkFault SFSailfish \
            SFSailfishFault Sailfish SailfishFault; do \
        git worktree add ${branch} ${branch}; \
    done
WORKDIR /home/user/CCS2025-artifact/formal-specifications
RUN make tla2tools.jar

WORKDIR /home/user/CCS2025-artifact

RUN python3 -m venv /home/user/venv
ENV PATH="/home/user/venv/bin:${PATH}"
RUN pip3 install -r /home/user/CCS2025-artifact/SFSailfish/benchmark/requirements.txt

#USER root
# reduce image size
#RUN rm -rf /var/lib/apt/lists/*
#USER user

WORKDIR /home/user/

RUN mkdir /home/user/.ssh/
COPY dummy_key.json /home/user/key.json

# Default command is to start a shell interactively
CMD ["/bin/bash"]
