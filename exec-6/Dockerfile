# Build (compilação)
FROM golang:1.21-alpine AS builder

WORKDIR /app

# Copiar arquivos do projeto
COPY go.mod go.sum ./
RUN go mod download

# Copiar o código
COPY . .

# Compilar binário estático com otimizações
RUN CGO_ENABLED=0 GOOS=linux go build \
-ldflags="-s -w \ 
-o / docker-gs-ping

# Runtime
FROM alpine:3.19

WORKDIR /
# Copiar apenas o binário do estágio builder
COPY --from=builder /docker-gs-ping /docker-gs-ping
# Adicionar cerificados CA
RUN apk --no-cache add ca-certificates
# Porta exposta
EXPOSE 8080
# Usar usuário não-root
USER 1001:1001
# Ponto de entrada
ENTRYPOINT ["/docker-gs-ping"]