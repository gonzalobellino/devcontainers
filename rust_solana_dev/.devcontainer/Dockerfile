# Node LTS
FROM mcr.microsoft.com/devcontainers/javascript-node:22

ENV DEBIAN_FRONTEND=noninteractive
ENV HOME=/home/node
ENV ZSH=${HOME}/.oh-my-zsh

# --- Versiones pinneables ---
ARG SOLANA_INSTALL_URL="https://release.anza.xyz/v2.1.0/install"
ARG ANCHOR_VERSION="0.31.0"

# Usa bash para los RUN (evita edge cases con scripts)
SHELL ["/bin/bash", "-lc"]

USER root

# Herramientas & toolchain nativo
RUN apt-get update && apt-get install -y \
    curl wget unzip xz-utils git nano jq lsof net-tools procps htop sudo gnupg ca-certificates \
    build-essential pkg-config libssl-dev libudev-dev clang cmake \
    postgresql-client redis-tools \
 && curl -fsSL https://get.docker.com | sh \
 && apt-get clean && rm -rf /var/lib/apt/lists/*

# JS utils (opcionales)
RUN npm install -g npm@latest npm-check-updates serve ts-node eslint prettier

# Docker group
RUN usermod -aG docker node

# ---- Usuario node: Rust + Solana(Agave) + AVM/Anchor ----
USER node
WORKDIR /home/node

# Rust stable + componentes
ENV CARGO_HOME=/home/node/.cargo
ENV RUSTUP_HOME=/home/node/.rustup
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y --profile minimal --default-toolchain stable
RUN ~/.cargo/bin/rustup component add rustfmt clippy rust-src
RUN ~/.cargo/bin/cargo install cargo-edit cargo-watch sccache

# Instalar Agave/Solana (2.1.x)
RUN sh -c "$(curl -sSfL ${SOLANA_INSTALL_URL})"

# AVM desde coral-xyz + preparar directorio
ENV AVM_HOME=/home/node/.avm
RUN mkdir -p "$AVM_HOME/bin" 
RUN ~/.cargo/bin/cargo install --git https://github.com/coral-xyz/anchor avm --force --locked
RUN ~/.cargo/bin/avm install ${ANCHOR_VERSION}
RUN ~/.cargo/bin/avm use ${ANCHOR_VERSION}
 

# --- Plan B (alternativo, por si AVM diera problemas) ---
# RUN ~/.cargo/bin/cargo install --git https://github.com/coral-xyz/anchor --tag v${ANCHOR_VERSION} anchor-cli --locked --force

# PATH persistente
RUN echo -e '\n# ---- Rust, Agave/Solana y AVM/Anchor ----' >> $HOME/.zshrc \
 && echo 'export PATH="$HOME/.cargo/bin:$HOME/.local/share/solana/install/active_release/bin:$HOME/.avm/bin:$PATH"' >> $HOME/.zshrc \
 && echo 'export PATH="$HOME/.cargo/bin:$HOME/.local/share/solana/install/active_release/bin:$HOME/.avm/bin:$PATH"' >> $HOME/.bashrc

# zsh plugins
RUN git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/zsh-autosuggestions && \
    git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting && \
    sed -i 's/plugins=(git)/plugins=(git npm npx docker docker-compose zsh-autosuggestions zsh-syntax-highlighting)/' $HOME/.zshrc && \
    sed -i 's/ZSH_THEME=.*/ZSH_THEME="agnoster"/' $HOME/.zshrc

WORKDIR /workspaces/
CMD ["sleep", "infinity"]
