FROM rocker/r-ver:4.4.2

# Instalar dependências do sistema
RUN apt-get update && apt-get install -y \
    build-essential \
    libcurl4-openssl-dev \
    libssl-dev \
    libxml2-dev \
    libgit2-dev \
    python3-pip \
    python3-dev \
    python3-venv \
    git \
    curl \
    vim \
    sudo \
    gnupg2 \
    apt-transport-https \
    ca-certificates \
    dirmngr \
    && rm -rf /var/lib/apt/lists/*

# Adicionar repositório oficial do ClickHouse
RUN curl -fsSL https://packages.clickhouse.com/CLICKHOUSE-KEY.GPG | gpg --dearmor -o /usr/share/keyrings/clickhouse-archive-keyring.gpg && \
    echo "deb [signed-by=/usr/share/keyrings/clickhouse-archive-keyring.gpg] https://packages.clickhouse.com/deb stable main" | tee /etc/apt/sources.list.d/clickhouse.list

# Instalar clickhouse-server e clickhouse-client
RUN apt-get update && apt-get install -y clickhouse-server clickhouse-client && rm -rf /var/lib/apt/lists/*

# Expor portas padrão do ClickHouse
EXPOSE 8123 9000

# Comando padrão para iniciar o servidor
CMD ["clickhouse-server", "--config-file=/etc/clickhouse-server/config.xml"]

# Criar link simbólico para python
RUN ln -sf /usr/bin/python3 /usr/local/bin/python

# Instalar Jupyter e ferramentas Python (usando --break-system-packages é seguro em containers)
RUN pip3 install --no-cache-dir --break-system-packages \
    jupyterlab \
    notebook \
    ipykernel \
    polars>=1.0.0 \
    radian \
    stemgraphic \
    matplotlib \
    seaborn \
    pandas \
    numpy \
    scipy \
    scikit-learn \
    plotly \
    pyarrow

# Instalar pacotes R necessários
RUN R -e "install.packages(c('IRkernel', 'languageserver'), repos='https://cloud.r-project.org', Ncpus=2)"

# Registrar o kernel R no Jupyter
RUN R -e "IRkernel::installspec(user = FALSE)"

# Criar usuário vscode (compatível com Codespaces)
RUN useradd -m -s /bin/bash vscode && \
    usermod -aG sudo vscode && \
    echo "vscode ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Configurar diretório de trabalho
WORKDIR /workspaces

# Mudar para usuário vscode
USER vscode
