FROM python:3.8

ARG config_path=./config-sagemaker.yaml

USER root

RUN apt-get -qq -y update && \
    apt-get -qq -y upgrade && \
    apt-get -y autoclean && \
    apt-get -y autoremove && \
    rm -rf /var/lib/apt/lists/*


COPY ${config_path} /root/server-config.yaml

ENV VIRTUAL_ENV=/venv
ENV PATH="$VIRTUAL_ENV/bin:$PATH"


RUN python3 -m venv $VIRTUAL_ENV && \
    pip3 install --no-cache-dir --upgrade pip && \
    pip3 install --no-cache-dir psutil && \
    pip3 install --no-cache-dir deepsparse-nightly && \
    pip3 install --no-cache-dir uvicorn && \
    pip3 install --no-cache-dir fastapi && \
    pip3 install --no-cache-dir python-multipart

# create 'serve' command for sagemaker entrypoint
RUN mkdir /opt/server/
RUN echo "#! /bin/bash" > /opt/server/serve
RUN echo "deepsparse.server config --port 8080 /root/server-config.yaml" >> /opt/server/serve
RUN chmod 777 /opt/server/serve

ENV PATH="/opt/server:${PATH}"
WORKDIR /opt/server

ENTRYPOINT ["bash", "/opt/server/serve"]
