FROM mcr.microsoft.com/vscode/devcontainers/base:debian

# Install additional OS packages
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
    build-essential \
    libhidapi-dev