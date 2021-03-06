# https://github.com/jupyter/docker-stacks
# https://github.com/jupyter-incubator/sparkmagic
# https://github.com/jupyter/docker-stacks/tree/master/scipy-notebook
#https://github.com/jupyter/docker-stacks/wiki
ARG BASE_CONTAINER=jupyter/scipy-notebook:6d42503c684f

FROM $BASE_CONTAINER

LABEL maintainer="Aliaksandr Sasnouskikh <jaahstreetlove@gmail.com>"

# Install Sparkmagic kernel
# https://github.com/jupyter-incubator/sparkmagic/blob/master/Dockerfile.jupyter
USER root

# This is needed because requests-kerberos fails to install on debian due to missing linux headers
RUN conda install requests-kerberos -y

#USER $NB_UID

RUN pip install --upgrade pip && \
    pip install --upgrade --ignore-installed setuptools
   # pip install sparkmagic==0.12.9

# Install Tensorflow
RUN pip install --quiet --no-cache-dir \
    'tensorflow==2.2.0' && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

#RUN conda install -c conda-forge tensorflow 
RUN conda install -c pytorch pytorch

COPY sparkmagic_tmp /home/$NB_USER/sparkmagic

RUN cd /home/$NB_USER/sparkmagic && pip install -e sparkmagic && pip install -e hdijupyterutils && \
    pip install -e autovizwidget
    
USER $NB_UID
RUN mkdir -p /home/$NB_USER/.sparkmagic
COPY sparkmagic_tmp/sparkmagic/example_config.json /home/$NB_USER/.sparkmagic/config.json

RUN pip install --quiet nbgitpuller

RUN jupyter nbextension enable --py --sys-prefix widgetsnbextension && \
    jupyter-kernelspec install --user $(pip show sparkmagic | grep Location | cut -d" " -f2)/sparkmagic/kernels/sparkkernel && \
    jupyter-kernelspec install --user $(pip show sparkmagic | grep Location | cut -d" " -f2)/sparkmagic/kernels/pysparkkernel && \
    jupyter serverextension enable --py sparkmagic

USER root
RUN chown $NB_USER /home/$NB_USER/.sparkmagic/config.json

# Misc
RUN mkdir -p /home/$NB_USER/notebooks && \
    chmod -R 777 /home/$NB_USER/notebooks

COPY entrypoint.sh /opt/
COPY singleuser-entrypoint.sh /opt/

ENTRYPOINT ["tini", "-g", "--"]
CMD [ "/opt/entrypoint.sh" ]

# Switch back to jovyan to avoid accidental container runs as root
USER $NB_UID
