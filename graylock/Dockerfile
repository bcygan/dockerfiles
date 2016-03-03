# All-in-One Graylog server following the instructions 
# at https://www.digitalocean.com/community/tutorials/how-to-install-graylog-1-x-on-ubuntu-14-04
FROM ubuntu:15.10
MAINTAINER Bernhard Cygan <bcygan@cloudbees.com>

# install Mongo DB
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv 7F0CEB10
RUN echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.0 multiverse" | tee /etc/apt/sources.list.d/mongodb-org-3.0.list
# 3.2 has a bad PUBKEY
#RUN echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | tee /etc/apt/sources.list.d/mongodb-org-3.2.list
RUN apt-get update && apt-get install -y mongodb-org

# install Java
RUN apt-get install -y software-properties-common python-software-properties
RUN /usr/bin/add-apt-repository ppa:webupd8team/java
RUN echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | /usr/bin/debconf-set-selections
RUN apt-get update && apt-get install -y oracle-java8-installer

# install Elasticsearch
RUN wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
RUN echo "deb http://packages.elastic.co/elasticsearch/1.7/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-1.7.x.list
RUN apt-get update && apt-get install -y elasticsearch

# now edit the configruation
RUN echo "need to configure ES"

# RUN service elasticsearch restart
# RUN update-rc.d elasticsearch defaults 95 10

# sleep a few seconds
# test that ES is running properly
RUN apt-get install curl
RUN curl -XGET 'http://localhost:9200/_cluster/health?pretty=true'

# install Graylog
RUN cd ~
RUN wget https://packages.graylog2.org/packages/graylog-1.3-repository-ubuntu14.04_latest.deb
RUN dpkg -i graylog-1.3-repository-ubuntu14.04_latest.deb
RUN apt-get update && apt-get install apt-transport-https graylog-server

# install pwgen for password generation
RUN apt-get install pwgen
RUN SECRET=$(pwgen -s 96 1)
RUN sudo -E sed -i -e 's/password_secret =.*/password_secret = '$SECRET'/' /etc/graylog/server/server.conf
RUN PASSWORD=$(echo -n password | shasum -a 256 | awk '{print $1}')
RUN sudo -E sed -i -e 's/root_password_sha2 =.*/root_password_sha2 = '$PASSWORD'/' /etc/graylog/server/server.conf

# configure Graylog

# RUN Graylog Server
RUN sudo start graylog-server

# install Graylog Web
RUN sudo apt-get install graylog-web
