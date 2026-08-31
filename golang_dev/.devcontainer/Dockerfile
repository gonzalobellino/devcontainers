FROM golang:1.24.2-bookworm

ENV DEBIAN_FRONTEND=noninteractive
ENV LANG=C.UTF-8
ENV USER=dev

# Instalar herramientas útiles
RUN apt-get update && apt-get install -y \
  sudo locales zsh postgresql-client redis-tools docker.io vim wget unzip \
  curl gnupg ca-certificates jq xz-utils \
  && rm -rf /var/lib/apt/lists/*

# GitHub CLI
RUN curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg \
      | dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
 && chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
 && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" \
      > /etc/apt/sources.list.d/github-cli.list \
 && apt-get update \
 && apt-get install -y --no-install-recommends gh \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# Node.js LTS (para Vercel CLI)
RUN ARCH=$(dpkg --print-architecture) \
 && case "$ARCH" in amd64) NODE_ARCH=x64 ;; arm64) NODE_ARCH=arm64 ;; *) NODE_ARCH=$ARCH ;; esac \
 && NODE_VERSION=$(curl -fsSL https://nodejs.org/dist/index.json | jq -r '[.[] | select(.lts != false)][0].version') \
 && curl -fsSL "https://nodejs.org/dist/${NODE_VERSION}/node-${NODE_VERSION}-linux-${NODE_ARCH}.tar.xz" \
      | tar -xJ -C /usr/local --strip-components=1 \
 && node -v && npm -v \
 && npm install -g vercel@latest \
 && vercel --version

# Localización
RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && locale-gen

# Crear usuario
RUN useradd -m -s /bin/zsh $USER && echo "$USER ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

USER $USER
WORKDIR /home/$USER

# Instalar Oh My Zsh y plugins recomendados
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended && \
    ZSH_CUSTOM=${ZSH_CUSTOM:-/home/$USER/.oh-my-zsh/custom} && \
    git clone https://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions && \
    git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting && \
    git clone https://github.com/zsh-users/zsh-completions $ZSH_CUSTOM/plugins/zsh-completions && \
    git clone https://github.com/zsh-users/zsh-history-substring-search $ZSH_CUSTOM/plugins/zsh-history-substring-search && \
    git clone https://github.com/paulirish/git-open $ZSH_CUSTOM/plugins/git-open && \
    git clone https://github.com/direnv/direnv $ZSH_CUSTOM/plugins/zsh-direnv && \
    git clone https://github.com/jeffreytse/zsh-vi-mode $ZSH_CUSTOM/plugins/zsh-vi-mode && \
    sed -i 's/plugins=(git)/plugins=(git docker docker-compose zsh-autosuggestions zsh-syntax-highlighting zsh-completions zsh-history-substring-search git-open zsh-direnv zsh-vi-mode)/' ~/.zshrc && \
    sed -i 's/ZSH_THEME=.*/ZSH_THEME=\"agnoster\"/' ~/.zshrc
