FROM node:lts-slim AS app-env

# Install dependencies
RUN apt-get update && apt-get install -y \
  python3 \
  python3-pip \
  openjdk-17-jre-headless \
  bash \
  wget \
  curl \
  ca-certificates \
  fonts-liberation \
  libappindicator3-1 \
  libasound2 \
  libatk-bridge2.0-0 \
  libatk1.0-0 \
  libcups2 \
  libdbus-1-3 \
  libdrm2 \
  libgbm1 \
  libgtk-3-0 \
  libnspr4 \
  libnss3 \
  libx11-xcb1 \
  libxcomposite1 \
  libxdamage1 \
  libxrandr2 \
  libx11-6 \
  libxext6 \
  libxfixes3 \
  libxrender1 \
  libxi6 \
  libxtst6 \
  libcairo2 \
  libgdk-pixbuf2.0-0 \
  libpango-1.0-0 \
  udev \
  --no-install-recommends && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/*

# Pre-cache emulator dependencies
RUN npm install -g firebase-tools && \
  firebase setup:emulators:database && \
  firebase setup:emulators:firestore && \
  firebase setup:emulators:pubsub && \
  firebase setup:emulators:storage && \
  firebase setup:emulators:ui

ENTRYPOINT [ "firebase" ]