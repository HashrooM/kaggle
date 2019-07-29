# TODO


# 1. Docker imageのpull
公式のpythonのimageを使いたかった。  
Docker Hubで調べたらpythonをpullすると公式のが引っ張れるらしい。  
さらに、TAGでslimを指定するとpythonを使うのに必要最低限の環境が出きるっぽい。  
機械学習とかやるのに安定していると思われる3.6のpythonを使うことにする。で、slim。  

``` Bash
$ docker pull python:3.6-slim
$ docker run -itd --name python python:3.6-slim
$ docker exec -it python /bin/bash
> python
Python 3.6.8 (default, Jun 11 2019, 01:21:42)
[GCC 6.3.0 20170516] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> quit()
> pip --version
pip 19.1.1 from /usr/local/lib/python3.6/site-packages/pip (python 3.6)
```
とりあえずpythonとpipは入ってる。このイメージを元に環境を作成していく。  

# 2. Dockerfileとrequirements.txtの作成
``` Dockerfile
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
```

```requirements.txt
numpy
scipy
pandas
scikit_learn
matplotlib
seaborn
jupyter
tensorflow
keras
pillow
pydotplus
```

# 3. Docker imageのBuild
imageの名前はkaggle_tensorflow:0.1としとく。  

```Bash
$ cd c/Users/moroh/work/kaggle
$ docker build ./ -t kaggle_tensorflow:0.1
...
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
kaggle_tensorflow   0.1                 4c4dc4bfd942        22 seconds ago      1.43GB
python              3.6-slim            73ba0dc9fc6c        3 weeks ago         138MB
ubuntu              latest              7698f282e524        7 weeks ago         69.9MB
hello-world         latest              fce289e99eb9        6 months ago        1.84kB
```

ちゃんとできた。  

# 4. Docker Hubへpush
練習ということでDocker Hubへpushする。  

```Bash
$ docker tag 4c4dc4bfd942 shibataro/kaggle_tensorflow:0.1 # まずはイメージにタグ付け
$ docker images
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
kaggle_tensorflow             0.1                 4c4dc4bfd942        10 minutes ago      1.43GB
shibataro/kaggle_tensorflow   0.1                 4c4dc4bfd942        10 minutes ago      1.43GB
python                        3.6-slim            73ba0dc9fc6c        3 weeks ago         138MB
ubuntu                        latest              7698f282e524        7 weeks ago         69.9MB
hello-world                   latest              fce289e99eb9        6 months ago        1.84kB

$ docker push shibataro/kaggle_tensorflow:0.1
```

Docker Hubのpublic repositoryに登録された。  
