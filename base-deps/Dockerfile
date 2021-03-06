# The base-deps Docker image installs main libraries needed to run Ray

# The GPU option is nvidia/cuda:10.1-cudnn7-runtime-ubuntu18.04
ARG BASE_IMAGE="arm64v8/ubuntu:focal"
FROM ${BASE_IMAGE}
# If this arg is not "autoscaler" then no autoscaler requirements will be included
ARG AUTOSCALER="autoscaler"
ENV TZ=America/Los_Angeles
# TODO(ilr) $HOME seems to point to result in "" instead of "/home/ray"
ENV PATH "/home/ray/pip/bin:$PATH"
ARG DEBIAN_FRONTEND=noninteractive
ARG PYTHON_VERSION=3.7.7

ARG RAY_UID=1000
ARG RAY_GID=100

RUN apt-get update -y \
    && apt-get install -y sudo tzdata \
    && useradd -ms /bin/bash -d /home/ray ray --uid $RAY_UID --gid $RAY_GID \
    && usermod -aG sudo ray \
    && echo 'ray ALL=NOPASSWD: ALL' >> /etc/sudoers \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean

USER $RAY_UID
ENV HOME=/home/ray

RUN sudo apt-get update -y && sudo apt-get upgrade -y \
    && sudo apt-get install -y \
        git \
        wget \
        cmake \
	make \
        $(if [ "$AUTOSCALER" = "autoscaler" ]; then echo \
        tmux \
        screen \
        rsync \
        openssh-client \
	python3-pip \
        gnupg; fi) \
    && python3 -m pip install --no-cache-dir \
        flatbuffers \
        cython==0.29.0 \
        numpy==1.15.4 \
        psutil \
        blist \ 
    # blist is needed for numpy (which is re-installed when ray is installed)
    # To avoid the following error on Jenkins:
    # AttributeError: 'numpy.ufunc' object has no attribute '__module__'
    && python3 -m pip uninstall -y dask \ 
    # We install cmake temporarily to get psutil
    && sudo apt-get autoremove -y cmake \
    # Either install kubectl or remove wget 
    && (if [ "$AUTOSCALER" = "autoscaler" ]; \
        then wget -O - -q https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add - \
        && sudo touch /etc/apt/sources.list.d/kubernetes.list \
        && echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list \
        && sudo apt-get update \
        && sudo apt-get install kubectl; \
    else sudo apt-get autoremove -y wget; \
    fi;) \
    && sudo rm -rf /var/lib/apt/lists/* \
    && sudo apt-get clean
