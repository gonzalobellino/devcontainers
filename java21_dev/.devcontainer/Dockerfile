FROM eclipse-temurin:21-jdk

ENV DEBIAN_FRONTEND=noninteractive
ENV LANG=C.UTF-8
ENV USER=ubuntu

RUN apt-get update && apt-get install -y \
  sudo zsh vim wget curl git unzip zip \
  locales postgresql-client redis-tools docker.io \
  maven openssl \
  && rm -rf /var/lib/apt/lists/*

RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && locale-gen

USER ${USER}

WORKDIR /home/${USER}

# Instalar Oh My Zsh + plugins
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended && \
    ZSH_CUSTOM=${ZSH_CUSTOM:-/home/$USER/.oh-my-zsh/custom} && \
    git clone https://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions && \
    git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting && \
    git clone https://github.com/zsh-users/zsh-completions $ZSH_CUSTOM/plugins/zsh-completions && \
    git clone https://github.com/zsh-users/zsh-history-substring-search $ZSH_CUSTOM/plugins/zsh-history-substring-search && \
    git clone https://github.com/paulirish/git-open $ZSH_CUSTOM/plugins/git-open && \
    git clone https://github.com/jeffreytse/zsh-vi-mode $ZSH_CUSTOM/plugins/zsh-vi-mode && \
    sed -i 's/plugins=(git)/plugins=(git docker docker-compose zsh-autosuggestions zsh-syntax-highlighting zsh-completions zsh-history-substring-search git-open zsh-vi-mode)/' ~/.zshrc && \
    sed -i 's/ZSH_THEME=.*/ZSH_THEME=\"agnoster\"/' ~/.zshrc
