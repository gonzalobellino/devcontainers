FROM mcr.microsoft.com/devcontainers/javascript-node:20

ENV DEBIAN_FRONTEND=noninteractive
ENV HOME=/home/node
ENV ZSH=${HOME}/.oh-my-zsh

USER root

# Herramientas extra útiles y Docker CLI
RUN apt-get update && apt-get install -y \
    curl wget unzip nano jq lsof net-tools procps htop sudo gnupg ca-certificates \
    && curl -fsSL https://get.docker.com | sh \
    && npm install -g yarn pnpm npm-check-updates serve ts-node eslint prettier \
    && apt-get clean

RUN apt-get update && apt-get install -y \
    postgresql-client \
    redis-tools

RUN npm install -g npm@11.3.0

# Agrega al usuario node al grupo docker
RUN usermod -aG docker node

# Pasamos al usuario node para configurar su shell
USER node

# Instala plugins adicionales para oh-my-zsh
RUN git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/zsh-autosuggestions && \
    git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting && \
    sed -i 's/plugins=(git)/plugins=(git npm npx docker docker-compose zsh-autosuggestions zsh-syntax-highlighting)/' $HOME/.zshrc && \
    sed -i 's/ZSH_THEME=.*/ZSH_THEME="agnoster"/' $HOME/.zshrc

WORKDIR /workspaces/
CMD ["sleep", "infinity"]
