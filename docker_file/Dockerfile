FROM python:3.6-slim

# install apps by apt
RUN apt-get update \
&& apt-get upgrade -y \
&& apt-get install -y vim graphviz git

# install python modules by pip
ADD ./requirements.txt /tmp
RUN pip install pip setuptools -U \
&& pip install -r /tmp/requirements.txt

# set working directory
WORKDIR /home/

# config and clean up
RUN ldconfig \
&& apt-get clean \
&& apt-get autoremove
