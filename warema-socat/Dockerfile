ARG BUILD_FROM=hassioaddons/base:edge
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Install deps & remove system node/npm (falls vorhanden)
RUN apk add --no-cache --virtual .build-deps python3 make g++ linux-headers curl && \
    apk del nodejs npm || true

# Install Node.js 18 (musl)
ENV NODE_VERSION=18.19.1
RUN curl -fsSL https://unofficial-builds.nodejs.org/download/release/v${NODE_VERSION}/node-v${NODE_VERSION}-linux-x64-musl.tar.xz \
    | tar -xJ -C /usr/local --strip-components=1 --no-same-owner && \
    ln -sf /usr/local/bin/node /usr/bin/node && \
    ln -sf /usr/local/bin/npm /usr/bin/npm && \
    node -v && npm -v

# Install runtime deps
RUN apk add --no-cache socat

# Copy package definitions
COPY rootfs/srv/package-lock.json /srv
COPY rootfs/srv/package.json /srv

WORKDIR /srv

# Install Node.js deps
RUN npm ci

# Clean up build deps
RUN apk del --purge .build-deps && rm -rf /root/.npm /root/.cache

# Copy rest of the rootfs
COPY rootfs/ /
