## 7. Crie uma rede Docker personalizada e faça dois containers, um Node.js e um MongoDB, se comunicarem, sugestão, utilize o projeto React Express + Mongo

- Criar um arquivo docker-compose.yml
```yaml
version: '3.8'

services:
  # Serviço do MongoDB
  mongodb:
    image: mongo:6.0
    container_name: mongodb
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: senhasegura
      MONGO_INITDB_DATABASE: meubanco
    volumes:
      - dados_mongo:/data/db
    networks:
      - rede_app
    # Segurança: Executa como usuário não-root (prática do MongoDB)
    user: "mongodb"

  # Backend (Node.js + Express)
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: backend
    environment:
      - NODE_ENV=development
      - MONGO_URI=mongodb://admin:senhasegura@mongodb:27017/meubanco?authSource=admin
    ports:
      - "5000:5000"
    depends_on:
      - mongodb
    networks:
      - rede_app
    volumes:
      - ./backend:/app
      - /app/node_modules

  # Frontend (React)
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: frontend
    ports:
      - "3000:3000"
    environment:
      - REACT_APP_API_URL=http://localhost:5000
    depends_on:
      - backend
    networks:
      - rede_app
    volumes:
      - ./frontend:/app
      - /app/node_modules
    # Habilita hot-reload para desenvolvimento
    stdin_open: true
    tty: true

# Redes e Volumes
networks:
  rede_app:
    driver: bridge

volumes:
  dados_mongo:
```

- Criar arquivo Dockerfile do Backend (Node.js + Express)
```dockerfile
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
```

- Criar um arquivo Dockerfile do Frontend (React)
```dockerfile
# Estágio 1: Construção
FROM node:18-alpine AS construtor

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Estágio 2: Execução (com usuário não-root)
FROM nginx:alpine

# Nginx já executa como não-root por padrão
RUN chown -R nginx:nginx /usr/share/nginx/html

COPY --from=construtor --chown=nginx /app/build /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 3000
CMD ["nginx", "-g", "daemon off;"]
```

- Construir e iniciar os containers:
```bash
docker-compose up -d --build
```

- Verificar containers em execução
```bash
docker-compose ps
```

- Verificar usuários não-root
```bash
docker exec backend whoami    # Deve mostrar "usuarioapp"
docker exec frontend whoami  # Deve mostrar "nginx"
docker exec mongodb whoami   # Deve mostrar "mongodb"
```

- Acessar os serviços
```bash
docker exec -it mongodb mongosh -u admin -p senhasegura
```

### Referências
[Building a Dockerized Node, Express, and MongoDB App with Docker Compose](https://medium.com/@muhammadnaqeeb/building-a-dockerized-node-express-and-mongodb-app-with-docker-compose-d6ec78e5897e)