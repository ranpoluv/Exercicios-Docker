FROM python:3.11-slim

# Instala pacotes do sistema necessários para compilar dependências do pip
RUN apt-get update && apt-get install -y --no-install-recommends \
    gcc \
    libffi-dev \
    libssl-dev \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Cria usuário não-root
RUN adduser --disabled-password --gecos "" appuser

WORKDIR /app

COPY requirements.txt .

# Atualiza pip e instala dependências do projeto
RUN pip install --upgrade pip setuptools \
    && pip install --no-cache-dir -r requirements.txt

COPY . .

USER appuser

CMD ["python", "app.py"]