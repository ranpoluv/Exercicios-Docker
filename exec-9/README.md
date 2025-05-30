## 9. Construa uma imagem baseada no Nginx ou Apache, adicionando um site HTML/CSS estático. Utilize a landing page do Creative Tim para criar uma página moderna hospedada no container.

- Clonar e entrar no repositório
```bash
git clone https://github.com/creativetimofficial/material-kit.git
cd material-kit
```

- Criar um arquivo Dockerfile
```bash
FROM nginx:stable
COPY material-kit /usr/share/nginx/html
EXPOSE 80
```

- Configurar o Nginx
```bash
server {
    listen 80;
    server_name localhost;

    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri /index.html;
    }

    # Arquivos estáticos
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
        expires max;
        log_not_found off;
    }

    # Negar acesso a arquivos ocultos
    location ~ /\. {
        deny all;
    }
}
```

- Construir a imagem
```bash
docker build -t landing-page-moderna
```

- Executar o container
```bash
docker run -d -p 8080:80 --name minha-landing-page landing-page-moderna
```

- Executar o container
```bash
docker run -d -p 8080:80 --name minha-landing-page landing-page-moderna
```

- Acessar a página

### Referências
[Publicando um web site estático na nuvem com Docker, Nginx e Azure Container Instances](https://renatogroffe.medium.com/publicando-um-web-site-est%C3%A1tico-na-nuvem-com-docker-nginx-e-azure-container-instances-d688823bbe1b)