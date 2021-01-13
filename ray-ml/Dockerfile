ARG GPU
FROM umbernhard/ray-arm"$GPU"

# We have to uninstall wrapt this way for Tensorflow compatibility
COPY requirements.txt ./
COPY requirements_ml_docker.txt ./
COPY requirements_rllib.txt ./
COPY requirements_tune.txt ./requirements_tune.txt

RUN sudo apt-get update \
    && sudo apt-get install -y gcc \
        cmake \
        libgtk2.0-dev \
        zlib1g-dev \
        libgl1-mesa-dev \
	libblas3 \
	liblapack3 \
	liblapack-dev \
	libblas-dev \
	gfortran \
	libatlas-base-dev \
    && python3 -m pip --no-cache-dir install -r requirements.txt \ 
    && python3 -m pip --no-cache-dir install -r requirements_ml_docker.txt \
    # Remove dataclasses & typing because they are included in Py3.7
    && python3 -m pip uninstall dataclasses typing -y  \
    && sudo rm requirements.txt && sudo rm requirements_ml_docker.txt \
    && sudo rm requirements_tune.txt && sudo rm requirements_rllib.txt \
    && sudo apt-get clean

