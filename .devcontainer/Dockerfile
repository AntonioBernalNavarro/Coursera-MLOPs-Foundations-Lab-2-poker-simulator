# Use Bullseye for better compatibility with the pinned 2022 dependencies
FROM mcr.microsoft.com/devcontainers/python:1-3.10-bullseye

# Remove the Yarn APT source: its GPG key has expired and breaks apt-get update
RUN rm -f /etc/apt/sources.list.d/yarn.list

# Now apt-get update works, and we can install make for the Makefile targets
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends make \
    && apt-get clean && rm -rf /var/lib/apt/lists/*
