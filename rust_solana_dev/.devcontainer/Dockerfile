# Node LTS (22)
FROM mcr.microsoft.com/devcontainers/javascript-node:22

ENV DEBIAN_FRONTEND=noninteractive
ENV HOME=/home/node
ENV ZSH=${HOME}/.oh-my-zsh

# ---- Build args para pinnear versiones facilmente ----
ARG SOLANA_INSTALL_URL="https://release.anza.xyz/v2.1.0/install"  # cambia a v2.1.x que prefieras
ARG ANCHOR_VERSION="0.31.0"                                       # cambia si necesitás otra versión

USER root

# Herramientas, toolchain nativo y clientes DB (+xz-utils y git para AVM/Anchor)
RUN apt-get update && apt-get install -y \
    curl wget unzip xz-utils git nano jq lsof net-tools procps htop sudo gnupg ca-certificates \
    build-essential pkg-config libssl-dev libudev-dev clang cmake \
    postgresql-client redis-tools \
 && curl -fsSL https://get.docker.com | sh \
 && apt-get clean && rm -rf /var/lib/apt/lists/*

# (opcional) utilidades JS globales mínimas
RUN npm install -g npm@latest npm-check-updates serve ts-node eslint prettier

# Docker group para 'node'
RUN usermod -aG docker node

# ---- Usuario node: Rust + Solana (Agave) + AVM/Anchor ----
USER node
WORKDIR /home/node

# Rust (stable) + componentes
ENV CARGO_HOME=/home/node/.cargo
ENV RUSTUP_HOME=/home/node/.rustup
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y --profile minimal --default-toolchain stable \
 && /home/node/.cargo/bin/rustup component add rustfmt clippy \
 && /home/node/.cargo/bin/cargo install cargo-edit cargo-watch sccache

# Agave/Solana 2.1.x (pinneado por URL)
RUN sh -c "$(curl -sSfL ${SOLANA_INSTALL_URL})"

# AVM desde coral-xyz/anchor + Anchor CLI pinneado
ENV AVM_HOME=/home/node/.avm
RUN /home/node/.cargo/bin/cargo install --git https://github.com/coral-xyz/anchor avm --force \
 && /home/node/.cargo/bin/avm install ${ANCHOR_VERSION} \
 && /home/node/.cargo/bin/avm use ${ANCHOR_VERSION}

# PATH persistente (zsh/bash)
RUN echo '\n# ---- Rust, Agave/Solana y AVM/Anchor ----' >> $HOME/.zshrc \
 && echo 'export PATH="$HOME/.cargo/bin:$HOME/.local/share/solana/install/active_release/bin:$HOME/.avm/bin:$PATH"' >> $HOME/.zshrc \
 && echo 'export PATH="$HOME/.cargo/bin:$HOME/.local/share/solana/install/active_release/bin:$HOME/.avm/bin:$PATH"' >> $HOME/.bashrc

# Plugins oh-my-zsh
RUN git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/zsh-autosuggestions && \
    git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting && \
    sed -i 's/plugins=(git)/plugins=(git npm npx docker docker-compose zsh-autosuggestions zsh-syntax-highlighting)/' $HOME/.zshrc && \
    sed -i 's/ZSH_THEME=.*/ZSH_THEME="agnoster"/' $HOME/.zshrc

WORKDIR /workspaces/
CMD ["sleep", "infinity"]
