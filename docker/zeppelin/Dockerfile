# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM ubuntu:20.04

LABEL maintainer="Parstat <florian.nika@parstat.md>"

ENV Z_VERSION="0.10.0"

ENV LOG_TAG="[ZEPPELIN_${Z_VERSION}]:" \
    ZEPPELIN_HOME="/opt/zeppelin" \
    HOME="/opt/zeppelin" \
    LANG=en_US.UTF-8 \
    LC_ALL=en_US.UTF-8 \
    JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64 \
    ZEPPELIN_ADDR="0.0.0.0"

RUN echo "$LOG_TAG install basic packages" && \
    apt-get -y update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y build-essential libcurl4-gnutls-dev libxml2-dev libssl-dev && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y locales language-pack-en tini openjdk-8-jre-headless wget

COPY requirements.txt /requirements.txt

#TODO comment if the image is build on a server
#COPY zeppelin-${Z_VERSION}-bin-all.tgz /tmp/zeppelin-${Z_VERSION}-bin-all.tgz

RUN echo "$LOG_TAG install python pip" && \
    apt-get -y install python3-pip &&\
    #ln -s /usr/bin/pip3 /usr/bin/pip &&\
    #ln -s /usr/bin/python3 /usr/bin/python &&\
    apt-get -y install python-is-python3 &&\
    pip install -r /requirements.txt

ENV DEBIAN_FRONTEND noninteractive

RUN echo "$LOG_TAG install r-base 3 " && \
    apt-get install r-base -y
    
 
#Install all needed libraires for R
RUN echo "$LOG_TAG install r packages needed for zeppelin" & \
    R -e "install.packages(c('base64enc', 'devtools', 'knitr', 'ggplot2', 'IRkernel', 'shiny'), repos = 'http://cran.r-project.org')"&&\
    R -e "install.packages(c('devtools','mplot', 'googleVis'), repos = 'http://cran.r-project.org'); require(devtools); install_github('ramnathv/rCharts')" &&\
    R -e "IRkernel::installspec()"

RUN echo "$LOG_TAG install data editing r packages" &\ 
    R -e "install.packages(c('validate', 'validatetools', 'errorlocate', 'univOutl', 'simputation', 'VIM', 'rspa', 'varhandle', 'SeleMix'), repos = 'http://cran.r-project.org')" 

# Cleanup
RUN echo "$LOG_TAG cleaning up " && \
    rm -rf /var/lib/apt/lists/* && \
    apt-get autoclean && \
    apt-get clean

RUN echo "$LOG_TAG Download Zeppelin binary" && \
    mkdir -p ${ZEPPELIN_HOME} && \
    #TODO uncomment when building in a server
    wget -nv -O /tmp/zeppelin-${Z_VERSION}-bin-all.tgz https://archive.apache.org/dist/zeppelin/zeppelin-${Z_VERSION}/zeppelin-${Z_VERSION}-bin-all.tgz && \
    tar --strip-components=1 -zxvf  /tmp/zeppelin-${Z_VERSION}-bin-all.tgz -C ${ZEPPELIN_HOME} && \
    rm -f /tmp/zeppelin-${Z_VERSION}-bin-all.tgz
    
RUN echo "$LOG_TAG Folder rights" && \
    chown -R root:root ${ZEPPELIN_HOME} && \
    mkdir -p ${ZEPPELIN_HOME}/logs ${ZEPPELIN_HOME}/run ${ZEPPELIN_HOME}/webapps && \
    # Allow process to edit /etc/passwd, to create a user entry for zeppelin
    chgrp root /etc/passwd && chmod ug+rw /etc/passwd && \
    # Give access to some specific folders
    #chmod -R 775 "${ZEPPELIN_HOME}/logs" "${ZEPPELIN_HOME}/run" "${ZEPPELIN_HOME}/notebook" "${ZEPPELIN_HOME}/conf" && \
    # Allow process to create new folders (e.g. webapps)
    chmod -R 775 ${ZEPPELIN_HOME}

COPY log4j.properties ${ZEPPELIN_HOME}/conf/
COPY log4j_docker.properties ${ZEPPELIN_HOME}/conf/
COPY shiro.ini ${ZEPPELIN_HOME}/conf/

USER 1000

EXPOSE 8080

ENTRYPOINT [ "/usr/bin/tini", "--" ]
WORKDIR ${ZEPPELIN_HOME}
CMD ["bin/zeppelin.sh"]
