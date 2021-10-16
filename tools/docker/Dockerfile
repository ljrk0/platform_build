FROM ubuntu:20.04
ARG userid
ARG groupid
ARG username

ENV DEBIAN_FRONTEND="noninteractive" TZ="UTC"

RUN apt-get update 

# https://source.android.com/setup/build/initializing#installing-required-packages-ubuntu-1804
RUN apt-get -y install git-core gnupg flex bison build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 libncurses5 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z1-dev libgl1-mesa-dev libxml2-utils xsltproc unzip fontconfig

# https://source.android.com/setup/develop#installing-repo
#RUN apt-get -y install repo
RUN apt-get -y install python3 python-is-python3
RUN REPO=$(mktemp /tmp/repo.XXXXXXXXX) && \
    curl -o ${REPO} https://storage.googleapis.com/git-repo-downloads/repo && \
    gpg --recv-key 8BB9AD793E8E6153AF0F9A4416530D5E920F5C65 && \
    curl -s https://storage.googleapis.com/git-repo-downloads/repo.asc | \
        gpg --verify - ${REPO} && \
    install -Dm 755 ${REPO} /usr/local/bin/repo


# OpenJDK/jar, jq, python-protobuf: android-prepare-vendor
# signify: required for signing factory images zips.
RUN apt-get -y install jq openjdk-11-jdk python-protobuf signify rsync

COPY gitconfig /root/.gitconfig
WORKDIR /src
ENTRYPOINT [ "/bin/bash", "-i" ]
