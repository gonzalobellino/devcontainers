# Node LTS (22) + Corepack (Yarn/Pnpm)
FROM mcr.microsoft.com/devcontainers/javascript-node:22

ENV DEBIAN_FRONTEND=noninteractive
ENV HOME=/home/node

# XDG / user-local paths
ENV XDG_CACHE_HOME=/home/node/.cache
ENV XDG_CONFIG_HOME=/home/node/.config
ENV XDG_DATA_HOME=/home/node/.local/share
ENV NPM_CONFIG_PREFIX=/home/node/.npm-global

# Android SDK
ENV ANDROID_HOME=/opt/android-sdk
ENV ANDROID_SDK_ROOT=/opt/android-sdk
ENV ANDROID_USER_HOME=/home/node/.android

# PATH
ENV PATH=/home/node/.local/bin:/home/node/.npm-global/bin:${ANDROID_HOME}/cmdline-tools/latest/bin:${ANDROID_HOME}/platform-tools:${ANDROID_HOME}/emulator:${PATH}

ENV PNPM_HOME=/home/node/.local/share/pnpm
ENV PATH=$PNPM_HOME/bin:/home/node/.venv/bin:/home/node/.npm-global/bin:/home/node/.local/bin:$PATH

USER root

# Herramientas útiles, Docker CLI, clientes DB y Java para Android
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    wget \
    unzip \
    nano \
    jq \
    lsof \
    net-tools \
    procps \
    htop \
    sudo \
    gnupg \
    ca-certificates \
    git \
    default-jdk \
    postgresql-client \
    redis-tools \
 && curl -fsSL https://get.docker.com | sh \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# Corepack para yarn/pnpm
RUN corepack enable \
 && corepack prepare yarn@stable --activate \
 && corepack prepare pnpm@latest --activate

# Android SDK command-line tools + paquetes base
RUN mkdir -p ${ANDROID_HOME}/cmdline-tools && \
    cd /tmp && \
    curl -fsSLo commandlinetools.zip https://dl.google.com/android/repository/commandlinetools-linux-14742923_latest.zip && \
    unzip -q commandlinetools.zip -d ${ANDROID_HOME}/cmdline-tools && \
    mv ${ANDROID_HOME}/cmdline-tools/cmdline-tools ${ANDROID_HOME}/cmdline-tools/latest && \
    rm -f commandlinetools.zip && \
    yes | sdkmanager --licenses >/dev/null && \
    sdkmanager --sdk_root=${ANDROID_HOME} \
      "platform-tools" \
      "cmdline-tools;latest" \
      "build-tools;35.0.0" \
      "platforms;android-35" \
      "emulator"

# Docker group para el usuario node
RUN groupadd -f docker && usermod -aG docker node

# Preparar directorios del usuario node
RUN mkdir -p \
    /home/node/.android \
    /home/node/.cache \
    /home/node/.config \
    /home/node/.local/share \
    /home/node/.local/bin \
    /home/node/.npm-global \
    /home/node/.claude \
    /home/node/.codex \
    /home/node/.cursor \
 && chown -R node:node \
    /home/node/.android \
    /home/node/.cache \
    /home/node/.config \
    /home/node/.local \
    /home/node/.npm-global \
    /home/node/.claude \
    /home/node/.codex \
    /home/node/.cursor \
    ${ANDROID_HOME}

RUN mkdir -p /home/node/.local/share/pnpm \
 && chown -R node:node /home/node/.local/share
 

RUN mkdir -p /commandhistory \
    && touch /commandhistory/.bash_history /commandhistory/.zsh_history \
    && chown -R node:node /commandhistory \
    && chmod 700 /commandhistory \
    && chmod 600 /commandhistory/.bash_history /commandhistory/.zsh_history    

USER node

# CLIs npm instalados como node
# Expo: usar npx expo desde cada proyecto; EAS sí global
RUN npm install -g \
    npm-check-updates \
    serve \
    ts-node \
    eslint \
    prettier \
    @openai/codex \
    eas-cli

RUN pnpm i -g vercel@latest

# Claude Code
RUN curl -fsSL https://claude.ai/install.sh | bash

# Cursor CLI / Agent
RUN curl -fsS https://cursor.com/install | bash

# Verificación mínima
RUN node -v && \
    npm -v && \
    pnpm -v && \
    yarn -v && \
    java -version && \
    adb version && \
    sdkmanager --version && \
    codex --version && \
    claude --version && \
    agent --help >/dev/null && \
    eas --version

WORKDIR /workspaces/
CMD ["sleep", "infinity"]