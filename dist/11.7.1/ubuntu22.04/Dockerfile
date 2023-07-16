ARG PYTHON_VERSION=3.9.16
ARG CUDA_TAG=11.7.1-cudnn8-devel-ubuntu22.04
FROM python:${PYTHON_VERSION}-slim-bullseye as python-build-stage

FROM nvidia/cuda:${CUDA_TAG} as base
COPY --from=python-build-stage /usr/local /usr/local
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && \
    apt-get -y install --no-install-recommends software-properties-common && \
    apt-get update && \
    apt-get -y install --no-install-recommends \
      curl \
      git && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
CMD ["sh", "-c", "tail -f /dev/null"]

FROM base as dev
RUN apt-get update && \
    apt-get -y install --no-install-recommends \
      bash-completion \
      gosu \
      graphviz \
      nano \
      tmux \
      tree \
      vim \
      xvfb \
      zip && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN echo source /usr/share/bash-completion/bash_completion >> /etc/bash.bashrc

WORKDIR /tmp
RUN curl -O http://nz2.archive.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.1_1.1.1f-1ubuntu2.19_amd64.deb && \
    dpkg -i libssl1.1_1.1.1f-1ubuntu2.19_amd64.deb
WORKDIR /

ENV POETRY_HOME=/opt/poetry
ENV PATH=${POETRY_HOME}/bin:${PATH}
RUN curl -sSL https://install.python-poetry.org | python3 - && \
    poetry completions bash >> /etc/bash_completion.d/poetry && \
    echo PATH="${PATH}" > /etc/environment && \
    echo source /etc/bash_completion.d/poetry >> /etc/bash.bashrc

RUN mkdir -m 777 /resources && \
    mkdir -m 777 /resources/dataset && \
    mkdir -m 777 /resources/models && \
    mkdir -m 777 /resources/reports

RUN rm -rf /tmp/*
EXPOSE 6006 8080 8888
