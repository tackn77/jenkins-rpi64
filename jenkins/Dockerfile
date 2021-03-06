FROM multiarch/ubuntu-core:arm64-focal

# start base
USER root

RUN apt-get update && apt-get upgrade -y
RUN apt-get install -y sudo \
     apt-transport-https \
     ca-certificates \
     curl \
     gnupg2 \
     software-properties-common

ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Tokyo
RUN sudo apt-get install -y tzdata

# install git java
RUN sudo apt-get install -y \
        git \
        openjdk-11-jdk

# install docker
RUN curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
RUN echo "deb [arch=arm64] https://download.docker.com/linux/ubuntu $(lsb_release -sc) stable" > /etc/apt/sources.list.d/docker-ce.list
RUN apt-get update && apt-get install -y docker-ce docker-ce-cli containerd.io

RUN curl -L https://github.com/docker/compose/releases/download/1.16.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
RUN chmod +x /usr/local/bin/docker-compose

# install jenkins
RUN curl -sSL https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
RUN echo 'deb https://pkg.jenkins.io/debian binary/'  >> /etc/apt/sources.list
RUN apt-get update && apt-get install -y jenkins

# add sudoers
RUN echo "jenkins ALL=(root) NOPASSWD: /usr/bin/docker" >> /etc/sudoers
RUN echo "jenkins ALL=(root) NOPASSWD: /etc/init.d/jenkins" >> /etc/sudoers
RUN echo "jenkins ALL=(root) NOPASSWD: /bin/bash" >> /etc/sudoers

# add docker group
# ARG DOCKER_GROUP_ID
# RUN groupmod -g ${DOCKER_GROUP_ID} docker && usermod -aG ${DOCKER_GROUP_ID} jenkins

COPY init.jenkins /etc/init.d/jenkins

EXPOSE 8080

WORKDIR /home/jenkins

RUN echo "#!/bin/bash" > start.sh && echo 'exec "$@"' >> start.sh && chmod +x start.sh
ENTRYPOINT ["/home/jenkins/start.sh"]

CMD ["/sbin/init"]

USER jenkins


