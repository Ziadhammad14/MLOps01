# Use a Jenkins image as base image
FROM jenkins/jenkins:lts

# Switch to root to install Docker
USER root

# Install prerequisites
RUN apt-get update && \
    apt-get install -y \
        apt-transport-https \
        ca-certificates \
        curl \
        gnupg2 \
        software-properties-common

# Add Docker’s official GPG key and repo (Debian-specific)
RUN mkdir -m 0755 -p /etc/apt/keyrings && \
    curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg && \
    echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
    https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker CLI (not the full Docker Engine)
RUN apt-get update && \
    apt-get install -y docker-ce-cli

# Create the 'docker' group explicitly
RUN groupadd docker && \
    usermod -aG docker jenkins

# Enable DinD
RUN mkdir -p /var/lib/docker
VOLUME /var/lib/docker
