# Devcontainer base con Node 22
FROM mcr.microsoft.com/devcontainers/javascript-node:22-bookworm

ENV DEBIAN_FRONTEND=noninteractive
ENV HOME=/home/node

# XDG paths para persistencia de agentes
ENV XDG_CACHE_HOME=/home/node/.cache
ENV XDG_CONFIG_HOME=/home/node/.config
ENV XDG_DATA_HOME=/home/node/.local/share

# npm global user install
ENV NPM_CONFIG_PREFIX=/home/node/.npm-global

# Python venv
ENV VENV_PATH=/home/node/.venv

# PATH
ENV PNPM_HOME=/home/node/.local/share/pnpm
ENV PATH=/home/node/.venv/bin:/home/node/.npm-global/bin:/home/node/.local/bin:/home/node/.local/share/pnpm:/home/node/.local/share/pnpm/bin:$PATH

USER root

# ================================
# Paquetes de sistema
# ================================
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
    build-essential \
    g++ \
    make \
    cmake \
    python3 \
    python3-pip \
    python3-venv \
    postgresql-client \
    redis-tools \
    libgtk-3-0 \
    libgbm1 \
    libxcomposite1 \
    libxdamage1 \
    libxrandr2 \
    libasound2 \
    libnss3 \
    libatk-bridge2.0-0 \
    libx11-xcb1 \
    libxshmfence1 \
    libsecret-1-0 \
    fonts-liberation \
    xdg-utils \
 && rm -rf /var/lib/apt/lists/*


# ================================
# Docker CLI
# ================================
RUN curl -fsSL https://get.docker.com | sh


# ================================
# GitHub CLI
# ================================
RUN curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg \
 | dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
 && chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
 && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" \
 > /etc/apt/sources.list.d/github-cli.list \
 && apt-get update \
 && apt-get install -y gh \
 && rm -rf /var/lib/apt/lists/*


# ================================
# docker group
# ================================
RUN groupadd -f docker && usermod -aG docker node

RUN corepack enable \
 && corepack prepare yarn@stable --activate \
 && corepack prepare pnpm@latest --activate
 
# ================================
# preparar dirs del usuario
# ================================
RUN mkdir -p \
    /home/node/.cache \
    /home/node/.config \
    /home/node/.local/share/pnpm \    
    /home/node/.local/share \
    /home/node/.npm-global \
    /home/node/.venv \
    /home/node/.claude \
    /home/node/.codex \
    /home/node/.cursor \
 && chown -R node:node \
 /home/node/.cache \
 /home/node/.config \
 /home/node/.local \
 /home/node/.npm-global \
 /home/node/.venv \
 /home/node/.claude \
 /home/node/.codex \
 /home/node/.cursor

 RUN mkdir -p /commandhistory \
    && touch /commandhistory/.bash_history /commandhistory/.zsh_history \
    && chown -R node:node /commandhistory \
    && chmod 700 /commandhistory \
    && chmod 600 /commandhistory/.bash_history /commandhistory/.zsh_history    


# ================================
# Usuario node
# ================================
USER node


# ================================
# Python virtualenv
# ================================
RUN python3 -m venv /home/node/.venv

ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PIP_NO_CACHE_DIR=1

RUN /home/node/.venv/bin/pip install -U pip setuptools wheel


# ================================
# Node package managers
# ================================
RUN npm install -g npm@latest

RUN pnpm i -g vercel@latest


# ================================
# CLI tools
# ================================
RUN npm install -g \
    npm-check-updates \
    serve \
    ts-node \
    eslint \
    prettier \
    @openai/codex


# ================================
# Claude Code
# ================================
RUN curl -fsSL https://claude.ai/install.sh | bash


# ================================
# Cursor CLI
# ================================
RUN curl -fsS https://cursor.com/install | bash


WORKDIR /workspaces/
CMD ["sleep", "infinity"]