FROM mcr.microsoft.com/azure-cli
# Install azcopy
RUN apt-get update && apt-get install -y --no-install-recommends rsync && \
    mkdir /tmp/azcopy && \
    curl -L https://aka.ms/downloadazcopyprlinux | tar zxv -C /tmp/azcopy && \
    /tmp/azcopy/install.sh && \
    rm -rf /tmp/azcopy