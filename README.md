# Traefik Proxy - Ambiente de Teste

Este projeto demonstra como configurar e utilizar o Traefik como proxy reverso para suas aplicações Docker. O Traefik facilita o roteamento de tráfego para diferentes serviços, oferecendo recursos como balanceamento de carga, SSL/TLS, autenticação básica e muito mais.

## Requisitos

- Docker
- Docker Compose

## Como Executar

### 1. Iniciar o Traefik

Primeiro, inicie o serviço Traefik que atuará como proxy reverso:

```bash
docker compose up -d
```

Isso iniciará o Traefik com:
- Dashboard administrativo na porta 8080
- Entrypoint principal na porta 8888

### 2. Iniciar Aplicações de Exemplo

Depois de iniciar o Traefik, você pode iniciar qualquer aplicação que deseja expor através dele. O projeto inclui um exemplo com a aplicação Wallos:

```bash
cd teste
docker compose up -d
```

## Acessando as Aplicações

- **Dashboard do Traefik**: http://localhost:8080
- **Wallos via Traefik**: http://localhost:8888/wallos
- **Wallos direto**: http://localhost:8282

## Configurando Novas Aplicações

Para adicionar novas aplicações ao Traefik, você precisa:

1. Garantir que a aplicação esteja na mesma rede Docker (`web`)
2. Adicionar as labels do Traefik no seu `docker-compose.yml`:

```yaml
services:
  your-app:
    # ... outras configurações ...
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.your-app.rule=PathPrefix(`/your-app`)"
      - "traefik.http.services.your-app.loadbalancer.server.port=80"
      - "traefik.http.middlewares.your-app-stripprefix.stripprefix.prefixes=/your-app"
      - "traefik.http.routers.your-app.middlewares=your-app-stripprefix@docker"
    networks:
      - web

networks:
  web:
    external: true
```

## Recursos Adicionais

O Traefik oferece vários recursos que podem ser configurados:

- **Autenticação Básica**: Proteja suas aplicações com autenticação básica
- **SSL/TLS**: Configure certificados SSL para HTTPS
- **Rate Limiting**: Limite o número de requisições
- **Circuit Breaker**: Proteja seus serviços contra sobrecarga

Para mais informações, consulte a [documentação oficial do Traefik](https://doc.traefik.io/traefik/).

## Exemplo: Wallos

O diretório `teste` contém um exemplo de configuração para a aplicação Wallos, demonstrando:

1. Como configurar as labels do Traefik
2. Como usar middlewares para manipular caminhos URL
3. Como compartilhar a rede Docker com o Traefik

Este exemplo pode ser usado como base para configurar suas próprias aplicações.
