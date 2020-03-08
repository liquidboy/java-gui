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



# ----------------------------------
# Java SDK 
# ----------------------------------
# from : https://github.com/oracle/docker-images/blob/master/OracleJava/java-11/Dockerfile
# 
# Requires physical file
#  jdk-13.XX_linux-x64_bin.tar.gz  --> .devcontainer folder
#     Download from 	https://www.oracle.com/technetwork/java/javase/downloads/jdk13-downloads-5066655.html
#	  SHA256 from 		https://www.oracle.com/webfolder/s/digest/13-0-2-checksum.html

# Default to UTF-8 file.encoding
ENV JAVA_VERSION=13.0.2
ENV LANG en_US.UTF-8
ENV JAVA_PKG=jdk-13.0.2_linux-x64_bin.tar.gz \
    JAVA_SHA256=e2214a723d611b4a781641061a24ca6024f2c57dbd9f75ca9d857cad87d9475f \
    JAVA_HOME=/usr/java/jdk-13

COPY $JAVA_PKG /tmp/jdk.tgz
RUN set -eux; \
	echo "$JAVA_SHA256 */tmp/jdk.tgz" | sha256sum -c -; \
	mkdir -p "$JAVA_HOME"; \
	tar --extract --file /tmp/jdk.tgz --directory "$JAVA_HOME" --strip-components 1; \
	rm /tmp/jdk.tgz; 

ENV	PATH $JAVA_HOME/bin:$PATH	

RUN set -eux; \
	yum install -y \
# JDK assumes freetype is available		
		freetype fontconfig \
	; \
	rm -rf /var/cache/yum; \
	ln -sfT "$JAVA_HOME" /usr/java/default; \
	ln -sfT "$JAVA_HOME" /usr/java/latest; \
	for bin in "$JAVA_HOME/bin/"*; do \
		base="$(basename "$bin")"; \
		[ ! -e "/usr/bin/$base" ]; \
		alternatives --install "/usr/bin/$base" "$base" "$bin" 20000; \
	done; \
# -Xshare:dump will create a CDS archive to improve startup in subsequent runs	
	java -Xshare:dump; \
	java --version; \
	javac --version