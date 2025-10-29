# Node LTS (22) + Corepack (Yarn/Pnpm)
FROM mcr.microsoft.com/devcontainers/javascript-node:22

ENV DEBIAN_FRONTEND=noninteractive
ENV HOME=/home/node
ENV ZSH=${HOME}/.oh-my-zsh

USER root

# Herramientas útiles, Docker CLI y clientes DB
RUN apt-get update && apt-get install -y \
    curl wget unzip nano jq lsof net-tools procps htop sudo gnupg ca-certificates \
    postgresql-client redis-tools \
 && curl -fsSL https://get.docker.com | sh \
 && apt-get clean && rm -rf /var/lib/apt/lists/*

# Gestores JS: npm al día + Corepack (recomendado)
# - Corepack habilita shims para yarn/pnpm sin instalarlos globalmente con npm
RUN npm install -g npm@latest npm-check-updates serve ts-node eslint prettier \
 && corepack enable \
 && corepack prepare yarn@stable --activate \
 && corepack prepare pnpm@latest --activate

# Docker group para el usuario node
RUN usermod -aG docker node

# ---- Usuario node: configuración de shell ----
USER node

# Plugins oh-my-zsh y tema
RUN git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/zsh-autosuggestions && \
    git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting && \
    sed -i 's/plugins=(git)/plugins=(git npm npx docker docker-compose zsh-autosuggestions zsh-syntax-highlighting)/' $HOME/.zshrc && \
    sed -i 's/ZSH_THEME=.*/ZSH_THEME="agnoster"/' $HOME/.zshrc

WORKDIR /workspaces/
CMD ["sleep", "infinity"]
