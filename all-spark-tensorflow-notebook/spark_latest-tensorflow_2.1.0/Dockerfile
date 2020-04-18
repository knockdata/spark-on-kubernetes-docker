# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
ARG BASE_CONTAINER=jupyter/all-spark-notebook
FROM $BASE_CONTAINER

LABEL maintainer="Jupyter Project <jupyter@googlegroups.com>"



USER root

# Install Tensorflow, check compatibility here: https://www.tensorflow.org/install/gpu 
RUN conda install --quiet --yes \
    'tensorflow=2.1*' \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER

# Install PyTorch with dependencies
RUN conda install --quiet --yes \
    pyyaml mkl mkl-include setuptools cmake cffi typing

# Check compatibility here: https://pytorch.org/get-started/locally/#mac-anaconda
RUN conda install --quiet --yes pytorch torchvision cpuonly -c pytorch

# Install elasticsearch libs
RUN apt-get update \
 && curl -sL https://repo1.maven.org/maven2/org/elasticsearch/elasticsearch-hadoop/6.8.1/elasticsearch-hadoop-6.8.1.jar
RUN pip install --no-cache-dir elasticsearch==7.1.0

# Install rpy2 to share data between Python and R
RUN conda install rpy2=2.9.4 plotly=4.4.1
RUN conda install -c conda-forge ipyleaflet

# Install important packages and Graphviz
RUN set -ex \
 && buildDeps=' \
    graphviz==0.11 \
' \
 && apt-get update \
 && apt-get -y install htop apt-utils graphviz libgraphviz-dev \
 && pip install --no-cache-dir $buildDeps

# Install various extensions
RUN jupyter labextension install @jupyterlab/github
RUN jupyter labextension install jupyterlab-drawio
RUN jupyter labextension install jupyter-leaflet
RUN jupyter labextension install @jupyterlab/plotly-extension
RUN jupyter labextension install @jupyter-widgets/jupyterlab-manager
RUN pip install --no-cache-dir jupyter-tabnine==1.0.2  && \
  jupyter nbextension install --py jupyter_tabnine && \
  jupyter nbextension enable --py jupyter_tabnine && \
  jupyter serverextension enable --py jupyter_tabnine
RUN fix-permissions $CONDA_DIR
RUN conda install -c conda-forge jupyter_contrib_nbextensions && \
  conda install -c conda-forge jupyter_nbextensions_configurator && \
  conda install -c conda-forge rise && \
  jupyter nbextension enable codefolding/main
RUN jupyter labextension install @ijmbarr/jupyterlab_spellchecker

RUN fix-permissions /home/$NB_USER

# Switch back to jovyan to avoid accidental container runs as root
USER $NB_UID
