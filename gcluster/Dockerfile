FROM golang:1.25.1-bookworm as builder

ARG GCLUSTER_VERSION

RUN apt-get update && apt-get install -y \
    git \
    make \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app
RUN git clone https://github.com/GoogleCloudPlatform/cluster-toolkit.git .
RUN [ -z "$GCLUSTER_VERSION" ] || git checkout "$GCLUSTER_VERSION"

RUN make gcluster

FROM gcr.io/cloud-builders/gcloud

RUN apt-get update && apt-get install -y gnupg software-properties-common curl wget         \
    && install -m 0755 -d /etc/apt/keyrings         \
    && curl -fsSL https://apt.releases.hashicorp.com/gpg | gpg --dearmor -o /etc/apt/keyrings/hashicorp.gpg         \
    && chmod a+r /etc/apt/keyrings/hashicorp.gpg         \
    && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/hashicorp.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/hashicorp.list > /dev/null         \
    && apt-get update \
    && apt-get install -y terraform packer         \
    && rm -rf /var/lib/apt/lists/*

COPY --from=builder /app/gcluster /usr/local/bin/gcluster

WORKDIR /workspace

COPY entrypoint.bash /builder/entrypoint.bash
ENTRYPOINT ["/builder/entrypoint.bash"]
