# Dockerfile para scraping, análisis con OpenAI, y shell zsh listo para Node/Python
FROM mcr.microsoft.com/devcontainers/javascript-node:20

ENV DEBIAN_FRONTEND=noninteractive
ENV HOME=/home/node
ENV ZSH=${HOME}/.oh-my-zsh
ENV PATH="/opt/venv/bin:$PATH"

USER root

# Herramientas útiles, Python, y clientes con entorno virtual para evitar errores por PEP 668 y soporte completo de Playwright
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl wget unzip nano jq lsof net-tools procps htop sudo gnupg ca-certificates \
    python3 python3-pip python3-venv \
    postgresql-client redis-tools \
    libgtk-4-1 libgtk-3-0 libgraphene-1.0-0 libgbm1 \
    libxcomposite1 libxdamage1 libxrandr2 libasound2 libnss3 \
    libatk-bridge2.0-0 libx11-xcb1 libxshmfence1 libsecret-1-0 \
    libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good \
    gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly gstreamer1.0-gl \
    gstreamer1.0-alsa gstreamer1.0-tools libvpx7 libopus0 \
    libwoff1 libharfbuzz-icu0 libenchant-2-2 libavif15 \
    libhyphen0 libmanette-0.2-0 libgles2-mesa libx264-dev \
    && python3 -m venv /opt/venv \
    && /opt/venv/bin/pip install --upgrade pip \
    && /opt/venv/bin/pip install openai langchain beautifulsoup4 requests playwright chromadb tiktoken \
    && curl -fsSL https://get.docker.com | sh \
    && apt-get clean && rm -rf /var/lib/apt/lists/*


    # Instalar herramientas globales de Node.js
RUN npm install -g yarn pnpm npm-check-updates serve ts-node eslint prettier \
    && npm install -g npm@11.3.0

# Usuario al grupo docker
RUN usermod -aG docker node

USER node

# Plugins ZSH adicionales
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
CMD ["sleep", "infinity"]