#FROM openjdk:8-jdk-slim
FROM ubuntu:18.04

# install operating system packages 
RUN apt-get update -y &&  apt-get install git curl gettext unzip wget software-properties-common python python3-pip dnsutils make -y 

## add more packages, if necessary
# install Java8
RUN add-apt-repository ppa:webupd8team/java -y && apt-get update && apt-get -y install openjdk-8-jdk-headless

# install boto3 library for PySpark applications to connect to S3
RUN pip3 install boto3==1.9


# use bpkg to handle complex bash entrypoints
# setting this env explicitly is required to get the bpkg install script working 
ENV USER=root
RUN curl -Lo- "https://raw.githubusercontent.com/bpkg/bpkg/master/setup.sh" | bash
RUN bpkg install cha87de/bashutil -g
## add more bash dependencies, if necessary 

ARG SPARK_VERSION_ARG=3.0.0-preview2

ENV BASE_IMAGE      openjdk:8-jdk-slim
ENV SPARK_VERSION   $SPARK_VERSION_ARG
ENV HADOOP_VERSION  hadoop3.2
ENV SCALA_VERSION   2.12

ENV SPARK_HOME      /opt/spark
ENV SPARK_CONF_DIR  $SPARK_HOME/conf
ENV PATH            $PATH:$SPARK_HOME/bin

# ENV PYTHONPATH ${SPARK_HOME}/python/lib/pyspark.zip:${SPARK_HOME}/python/lib/py4j-*.zip

### install spark
# git checkout 2.4.5
# dev/make-distribution.sh --name hadoop-3.1-cloud-scala-2.11 --pip --tgz -Phadoop-3.1 -Phadoop-cloud -Pkubernetes -DskipTests
#COPY bin/spark-${SPARK_VERSION}-bin-${HADOOP_VERSION}-scala-${SCALA_VERSION}.tgz /
# apache spark
RUN wget http://apache.mirrors.spacedump.net/spark/spark-3.0.0-preview2/spark-3.0.0-preview2-bin-hadoop3.2.tgz -O /tmp/spark-3.0.0-preview2-bin-hadoop3.2.tgz
#RUN  tar -xvzf /tmp/spark-3.0.0-preview2-bin-hadoop3.2.tgz -C /opt/

RUN tar -xzf /tmp/spark-${SPARK_VERSION}-bin-${HADOOP_VERSION}.tgz -C /opt/ && \
    ln -s /opt/spark-${SPARK_VERSION}-bin-${HADOOP_VERSION} $SPARK_HOME && \
    rm -f /tmp/spark-${SPARK_VERSION}-bin-${HADOOP_VERSION}.tgz && \
    mkdir -p $SPARK_HOME/work-dir && \
    mkdir -p $SPARK_HOME/spark-warehouse

# RUN mkdir ${SPARK_HOME}/python
# RUN apt install -y python python-pip && \
#     apt install -y python3 python3-pip && \
#     rm -r /usr/lib/python*/ensurepip && \
#     pip install --upgrade pip setuptools && \
#     rm -r /root/.cache && rm -rf /var/cache/apt/*

COPY conf/* $SPARK_CONF_DIR/
COPY entrypoint.sh /opt/
COPY Dockerfile /my_docker/

WORKDIR $SPARK_HOME/work-dir
ENTRYPOINT [ "/opt/entrypoint.sh" ]
