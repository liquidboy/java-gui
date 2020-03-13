#-------------------------------------------------------------------------------------------------------------
# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License. See https://go.microsoft.com/fwlink/?linkid=2090316 for license information.
#-------------------------------------------------------------------------------------------------------------
FROM oraclelinux:7-slim

# This Dockerfile adds a non-root user with sudo access. Use the "remoteUser"
# property in devcontainer.json to use it. On Linux, the container user's GID/UIDs
# will be updated to match your local UID/GID (when using the dockerFile property).
# See https://aka.ms/vscode-remote/containers/non-root-user for details.
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Set to false to skip installing zsh and Oh My ZSH!
ARG INSTALL_ZSH="true"

# Location and expected SHA for common setup script - SHA generated on release
ARG COMMON_SCRIPT_SOURCE="https://raw.githubusercontent.com/microsoft/vscode-dev-containers/master/script-library/common-redhat.sh"
ARG COMMON_SCRIPT_SHA="dev-mode"

# Configure apt and install packages
RUN yum update -y \
    && yum -y install gzip tar dialog wget ca-certificates 2>&1 \
    #
    # Verify git, common tools / libs installed, add/modify non-root user, optionally install zsh
    && wget -q -O /tmp/common-setup.sh $COMMON_SCRIPT_SOURCE \
    && if [ "$COMMON_SCRIPT_SHA" != "dev-mode" ]; then echo "$COMMON_SCRIPT_SHA /tmp/common-setup.sh" | sha256sum -c - ; fi \
    && /bin/bash /tmp/common-setup.sh "$INSTALL_ZSH" "$USERNAME" "$USER_UID" "$USER_GID" \
    && rm /tmp/common-setup.sh \
    #
    # Clean up
    && yum autoremove -y \
    && yum clean all -y


# https://staging.adoptopenjdk.net/630/installation.html?variant=openjdk11&jvmVariant=openj9#x64_linux-jdk
COPY adoptopenjdk.repo /etc/yum.repos.d/adoptopenjdk.repo

RUN yum update -y
RUN yum install adoptopenjdk-13-openj9 -y

RUN set -eux \
    && java -version \
    && javac -version

# javafx
# https://gluonhq.com/products/javafx/
# https://gluonhq.com/download/javafx-14-sdk-linux/

RUN yum install unzip -y

COPY openjfx-14_linux-x64_bin-sdk.zip /tmp/openjfx-14_linux-x64_bin-sdk.zip
RUN unzip -d /usr/lib/openjfx /tmp/openjfx-14_linux-x64_bin-sdk.zip
RUN rm /tmp/openjfx-14_linux-x64_bin-sdk.zip
ENV PATH_TO_FX=/usr/lib/openjfx/javafx-sdk-14/lib

COPY openjfx-14_linux-x64_bin-jmods.zip /tmp/openjfx-14_linux-x64_bin-jmods.zip
RUN unzip -d /usr/lib/openjfx /tmp/openjfx-14_linux-x64_bin-jmods.zip
RUN rm /tmp/openjfx-14_linux-x64_bin-jmods.zip
ENV PATH_TO_FX_MODS=/usr/lib/openjfx/javafx-jmods-14

# javafx samples
COPY install-samples.sh /tmp/install-samples.sh
RUN /bin/bash /tmp/install-samples.sh