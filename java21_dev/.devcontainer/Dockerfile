# Java 25 LTS (Eclipse Temurin)
FROM eclipse-temurin:25-jdk

ARG TARGETARCH
ENV DEBIAN_FRONTEND=noninteractive
ENV LANG=C.UTF-8
ENV USER=ubuntu
ENV HOME=/home/ubuntu

# XDG / user-local paths
ENV XDG_CACHE_HOME=/home/ubuntu/.cache
ENV XDG_CONFIG_HOME=/home/ubuntu/.config
ENV XDG_DATA_HOME=/home/ubuntu/.local/share
ENV NPM_CONFIG_PREFIX=/home/ubuntu/.npm-global
ENV SDKMAN_DIR=/home/ubuntu/.sdkman
ENV JMETER_HOME=/opt/jmeter
ENV PATH=/home/ubuntu/.local/bin:/home/ubuntu/.npm-global/bin:/home/ubuntu/.opencode/bin:/home/ubuntu/.cursor/bin:${JMETER_HOME}/bin:${PATH}

USER root

# Base tools + shell + DB clients
RUN apt-get update && apt-get install -y --no-install-recommends \
    sudo \
    zsh \
    vim \
    nano \
    wget \
    curl \
    git \
    unzip \
    zip \
    jq \
    fzf \
    tree \
    ripgrep \
    fd-find \
    htop \
    lsof \
    net-tools \
    procps \
    openssl \
    locales \
    ca-certificates \
    gnupg \
    postgresql-client \
    redis-tools \
    python3 \
    fontconfig \
 && echo "en_US.UTF-8 UTF-8" > /etc/locale.gen \
 && locale-gen \
 && ln -sf /usr/bin/fdfind /usr/local/bin/fd \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# Docker CLI (host docker.sock)
RUN curl -fsSL https://get.docker.com | sh

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

# Node.js LTS (for npm CLIs: codex, etc.) — binary oficial (más portable que nodesource)
RUN ARCH=$(dpkg --print-architecture) \
 && case "$ARCH" in amd64) NODE_ARCH=x64 ;; arm64) NODE_ARCH=arm64 ;; *) NODE_ARCH=$ARCH ;; esac \
 && NODE_VERSION=$(curl -fsSL https://nodejs.org/dist/index.json | jq -r '[.[] | select(.lts != false)][0].version') \
 && curl -fsSL "https://nodejs.org/dist/${NODE_VERSION}/node-${NODE_VERSION}-linux-${NODE_ARCH}.tar.xz" \
      | tar -xJ -C /usr/local --strip-components=1 \
 && node -v && npm -v

# kubectl
RUN ARCH=$(dpkg --print-architecture) \
 && curl -fsSLo /usr/local/bin/kubectl \
      "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/${ARCH}/kubectl" \
 && chmod +x /usr/local/bin/kubectl

# Helm
RUN curl -fsSL https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

# kind (Kubernetes IN Docker) — simulador multi-node
RUN ARCH=$(dpkg --print-architecture) \
 && curl -fsSLo /usr/local/bin/kind \
      "https://kind.sigs.k8s.io/dl/latest/kind-linux-${ARCH}" \
 && chmod +x /usr/local/bin/kind

# k3d (k3s in Docker) — cluster local ligero para práctica
RUN curl -fsSL https://raw.githubusercontent.com/k3d-io/k3d/main/install.sh | bash

# k9s (TUI para K8s)
RUN ARCH=$(dpkg --print-architecture) \
 && curl -fsSLo /tmp/k9s.tgz \
      "https://github.com/derailed/k9s/releases/latest/download/k9s_Linux_${ARCH}.tar.gz" \
 && tar -xzf /tmp/k9s.tgz -C /usr/local/bin k9s \
 && chmod +x /usr/local/bin/k9s \
 && rm -f /tmp/k9s.tgz

# kubectx / kubens
RUN git clone --depth 1 https://github.com/ahmetb/kubectx /opt/kubectx \
 && ln -sf /opt/kubectx/kubectx /usr/local/bin/kubectx \
 && ln -sf /opt/kubectx/kubens /usr/local/bin/kubens

# stern (multi-pod log tail)
RUN ARCH=$(dpkg --print-architecture) \
 && STERN_VERSION=$(curl -fsSL https://api.github.com/repos/stern/stern/releases/latest | jq -r .tag_name) \
 && STERN_VER=${STERN_VERSION#v} \
 && curl -fsSLo /tmp/stern.tgz \
      "https://github.com/stern/stern/releases/download/${STERN_VERSION}/stern_${STERN_VER}_linux_${ARCH}.tar.gz" \
 && tar -xzf /tmp/stern.tgz -C /usr/local/bin stern \
 && chmod +x /usr/local/bin/stern \
 && rm -f /tmp/stern.tgz

# k6 (load testing moderno)
RUN curl -fsSL https://dl.k6.io/key.gpg | gpg --dearmor -o /usr/share/keyrings/k6-archive-keyring.gpg \
 && echo "deb [signed-by=/usr/share/keyrings/k6-archive-keyring.gpg] https://dl.k6.io/deb stable main" \
      > /etc/apt/sources.list.d/k6.list \
 && apt-get update \
 && apt-get install -y --no-install-recommends k6 \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# Apache JMeter (CLI)
ARG JMETER_VERSION=5.6.3
RUN curl -fsSLo /tmp/jmeter.tgz \
      "https://downloads.apache.org/jmeter/binaries/apache-jmeter-${JMETER_VERSION}.tgz" \
    || curl -fsSLo /tmp/jmeter.tgz \
      "https://archive.apache.org/dist/jmeter/binaries/apache-jmeter-${JMETER_VERSION}.tgz" \
 && mkdir -p /opt \
 && tar -xzf /tmp/jmeter.tgz -C /opt \
 && ln -sfn "/opt/apache-jmeter-${JMETER_VERSION}" /opt/jmeter \
 && rm -f /tmp/jmeter.tgz \
 && ln -sf /opt/jmeter/bin/jmeter /usr/local/bin/jmeter \
 && ln -sf /opt/jmeter/bin/jmeter-server /usr/local/bin/jmeter-server

# httpie (API testing CLI, alternativa liviana a SoapUI)
RUN apt-get update \
 && apt-get install -y --no-install-recommends httpie \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# ngrok agent
RUN curl -fsSL https://ngrok-agent.s3.amazonaws.com/ngrok.asc \
      | gpg --dearmor -o /usr/share/keyrings/ngrok-archive-keyring.gpg \
 && echo "deb [signed-by=/usr/share/keyrings/ngrok-archive-keyring.gpg] https://ngrok-agent.s3.amazonaws.com bookworm main" \
      > /etc/apt/sources.list.d/ngrok.list \
 && apt-get update \
 && apt-get install -y --no-install-recommends ngrok \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# Usuario ubuntu (ya existe en Temurin) + docker group alineado al HOST + zsh default
# Umbrel host: /var/run/docker.sock → root:docker (GID 987). Sin este sync → permission denied.
ARG DOCKER_GID=987
RUN usermod -s /bin/zsh ubuntu \
 && if getent group docker >/dev/null; then groupmod -g "${DOCKER_GID}" docker; \
    else groupadd -g "${DOCKER_GID}" docker; fi \
 && usermod -aG docker,sudo ubuntu \
 && echo "ubuntu ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/ubuntu \
 && chmod 0440 /etc/sudoers.d/ubuntu

# Directorios de persistencia / configs
RUN mkdir -p \
    /home/ubuntu/.cache \
    /home/ubuntu/.config/ngrok \
    /home/ubuntu/.local/share \
    /home/ubuntu/.local/bin \
    /home/ubuntu/.npm-global \
    /home/ubuntu/.claude \
    /home/ubuntu/.codex \
    /home/ubuntu/.cursor \
    /home/ubuntu/.opencode \
    /home/ubuntu/.m2 \
    /home/ubuntu/.gradle \
    /home/ubuntu/.kube \
    /commandhistory \
 && touch /commandhistory/.bash_history /commandhistory/.zsh_history \
 && chown -R ubuntu:ubuntu /home/ubuntu /commandhistory \
 && chmod 700 /commandhistory /home/ubuntu/.config/ngrok \
 && chmod 600 /commandhistory/.bash_history /commandhistory/.zsh_history

USER ubuntu
WORKDIR /home/ubuntu

# Oh My Zsh + plugins más usados
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended \
 && ZSH_CUSTOM=${ZSH_CUSTOM:-/home/ubuntu/.oh-my-zsh/custom} \
 && git clone --depth 1 https://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions \
 && git clone --depth 1 https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting \
 && git clone --depth 1 https://github.com/zsh-users/zsh-completions $ZSH_CUSTOM/plugins/zsh-completions \
 && git clone --depth 1 https://github.com/zsh-users/zsh-history-substring-search $ZSH_CUSTOM/plugins/zsh-history-substring-search \
 && git clone --depth 1 https://github.com/paulirish/git-open $ZSH_CUSTOM/plugins/git-open \
 && git clone --depth 1 https://github.com/jeffreytse/zsh-vi-mode $ZSH_CUSTOM/plugins/zsh-vi-mode \
 && git clone --depth 1 https://github.com/romkatv/powerlevel10k.git $ZSH_CUSTOM/themes/powerlevel10k \
 && sed -i 's|ZSH_THEME=.*|ZSH_THEME="powerlevel10k/powerlevel10k"|' ~/.zshrc \
 && sed -i 's/plugins=(git)/plugins=(git docker docker-compose kubectl helm sudo extract colored-man-pages zsh-autosuggestions zsh-syntax-highlighting zsh-completions zsh-history-substring-search git-open zsh-vi-mode)/' ~/.zshrc \
 && printf '\n# Devcontainer history + PATH\nexport HISTFILE=/commandhistory/.zsh_history\nexport HISTSIZE=10000\nexport SAVEHIST=20000\nsetopt SHARE_HISTORY\nexport POWERLEVEL9K_DISABLE_CONFIGURATION_WIZARD=true\nexport PATH="$HOME/.local/bin:$HOME/.npm-global/bin:$HOME/.opencode/bin:$HOME/.cursor/bin:$PATH"\n# SDKMAN\n[[ -s "$HOME/.sdkman/bin/sdkman-init.sh" ]] && source "$HOME/.sdkman/bin/sdkman-init.sh"\n' >> ~/.zshrc

# SDKMAN → Maven + Gradle (últimas estables)
RUN curl -fsSL "https://get.sdkman.io" | bash \
 && bash -c 'export SDKMAN_DIR="$HOME/.sdkman" \
    && source "$HOME/.sdkman/bin/sdkman-init.sh" \
    && export SDKMAN_AUTO_ANSWER=true \
    && sdk install maven \
    && sdk install gradle \
    && sdk flush archives \
    && sdk flush temp'

# CLIs de agentes / tooling
RUN npm install -g @openai/codex vercel@latest \
 && curl -fsSL https://claude.ai/install.sh | bash \
 && curl -fsS https://cursor.com/install | bash \
 && curl -fsSL https://opencode.ai/install | bash

# Verificación mínima
RUN java -version \
 && bash -lc 'source "$HOME/.sdkman/bin/sdkman-init.sh" && mvn -v && gradle -v' \
 && node -v && npm -v \
 && gh --version \
 && kubectl version --client \
 && helm version \
 && kind version \
 && k3d version \
 && k9s version \
 && k6 version \
 && jmeter --version \
 && http --version \
 && codex --version \
 && claude --version \
 && agent --help >/dev/null \
 && opencode --version \
 && ngrok version \
 && vercel --version

WORKDIR /workspaces
CMD ["sleep", "infinity"]
