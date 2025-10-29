# Devcontainer base (Debian bookworm) con Node 20
FROM mcr.microsoft.com/devcontainers/javascript-node:20-bookworm

ENV DEBIAN_FRONTEND=noninteractive
ENV HOME=/home/node

USER root

# Paquetes de sistema (sin librerías Python del proyecto)
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl wget unzip nano jq lsof net-tools procps htop sudo gnupg ca-certificates git \
    build-essential g++ make libomp-dev cmake \
    python3 python3-pip python3-venv \
    postgresql-client redis-tools \
    # deps típicas headless (por si más adelante usás browsers)
    libgtk-3-0 libgbm1 libxcomposite1 libxdamage1 libxrandr2 libasound2 libnss3 \
    libatk-bridge2.0-0 libx11-xcb1 libxshmfence1 libsecret-1-0 \
    libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good \
    gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly gstreamer1.0-gl \
    gstreamer1.0-alsa gstreamer1.0-tools \
    fonts-liberation xdg-utils \
 && rm -rf /var/lib/apt/lists/*

# Herramientas Node globales
RUN npm install -g yarn pnpm npm-check-updates serve ts-node eslint prettier \
 && npm install -g npm@11.3.0

# (Opcional) Grupo docker para usar el daemon del host (docker-from-host)
RUN groupadd -f docker && usermod -aG docker node

# GitHub CLI (útil en proyectos)
RUN curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg \
    | dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
 && chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
 && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" \
    > /etc/apt/sources.list.d/github-cli.list \
 && apt-get update && apt-get install -y --no-install-recommends gh \
 && rm -rf /var/lib/apt/lists/*

# === A partir de acá, todo como usuario node ===
USER node

# Crear venv del usuario y ponerlo primero en PATH
RUN python3 -m venv /home/node/.venv
ENV VENV_PATH=/home/node/.venv
ENV PATH="/home/node/.venv/bin:$PATH"
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PIP_NO_CACHE_DIR=1

# Asegurar pip/setuptools/wheel actualizados (sin libs de proyecto)
RUN /home/node/.venv/bin/pip install -U pip setuptools wheel

# ZSH plugins (la imagen base trae oh-my-zsh)
RUN git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/zsh-autosuggestions && \
    git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting && \
    git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/zsh-completions && \
    git clone https://github.com/zsh-users/zsh-history-substring-search ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/zsh-history-substring-search && \
    git clone https://github.com/lukechilds/zsh-nvm ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/zsh-nvm && \
    git clone https://github.com/paulirish/git-open ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/git-open && \
    git clone https://github.com/jeffreytse/zsh-vi-mode ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/zsh-vi-mode && \
    sed -i 's/plugins=(git)/plugins=(git npm docker docker-compose zsh-autosuggestions zsh-syntax-highlighting zsh-completions zsh-history-substring-search zsh-nvm git-open zsh-vi-mode)/' $HOME/.zshrc && \
    sed -i 's/ZSH_THEME=.*/ZSH_THEME="agnoster"/' $HOME/.zshrc

WORKDIR /workspaces/
