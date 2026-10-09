FROM python:3.12-slim

# ssh client is required by Ansible to reach the server
RUN apt-get update \
 && apt-get install -y --no-install-recommends openssh-client \
 && rm -rf /var/lib/apt/lists/*

# The "ansible" package includes the community.docker collection
RUN pip install --no-cache-dir ansible

WORKDIR /ansible
