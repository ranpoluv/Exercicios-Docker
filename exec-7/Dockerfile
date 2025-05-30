# Estágio 1: Construção
FROM node:18-alpine AS construtor

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .

# Estágio 2: Execução (com usuário não-root)
FROM node:18-alpine

WORKDIR /app

# Cria usuário não-root
RUN adduser -D usuarioapp && \
    chown -R usuarioapp:usuarioapp /app

# Copia do estágio de construção
COPY --from=construtor --chown=usuarioapp /app /app

USER usuarioapp

EXPOSE 5000
CMD ["npm", "start"]