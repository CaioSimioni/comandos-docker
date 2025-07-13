# Comandos Docker Essenciais

Aqui é um compilado dos principais comandos docker que utilizo quase todos os dias. Última Atualização: 13 de Julho de 2025.

---

## 📖 Índice

- [🔧 Instalação](#-instalação)
  - [Linux/Debian/Ubuntu](#linuxdebianubuntu)
  - [Linux/Arch/Manjaro](#linuxarchmanjaro)
  - [Windows](#windows)
- [🐳 Containers Isolados](#-containers-isolados)
- [🎛 Docker Compose](#-docker-compose)
- [🧹 Limpeza](#-limpeza)
- [🔍 Inspeção](#-inspeção)
- [🩺 Healthcheck (Exemplo)](#-healthcheck-exemplo)
- [⚡ Dicas Rápidas](#-dicas-rápidas)

---

## 🔧 Instalação

### Linux/Debian/Ubuntu

1. Atualiza o sistema

    ```bash
    sudo apt update && sudo apt upgrade -y
    ```

2. Instala dependências necessárias

    ```bash
    sudo apt install -y ca-certificates curl gnupg lsb-release
    ```

3. Adiciona a chave GPG oficial do Docker

    ```bash
    sudo mkdir -p /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    ```

4. Adiciona o repositório do Docker

    ```bash
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
    https://download.docker.com/linux/debian $(lsb_release -cs) stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    ```

5. Atualiza os repositórios

    ```bash
    sudo apt update
    ```

6. Instala o Docker Engine, CLI, Buildx e Compose Plugin

    ```bash
    sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```

7. Adiciona o usuário atual ao grupo docker (opcional)

    ```bash
    sudo usermod -aG docker $USER
    ```

8. Testa a instalação do Docker

    ```bash
    docker run hello-world
    ```

    ![Exemplo de Teste Docker](imagens/teste-docker.png)

### Linux/Arch/Manjaro

1. Atualiza o sistema

    ```bash
    sudo pacman -Syu
    ```

2. Instala o Docker e plugins necessários

    ```bash
    sudo pacman -S --noconfirm docker docker-buildx docker-compose
    ```

3. Inicia e habilita o serviço Docker

    ```bash
    sudo systemctl enable --now docker
    ```

4. Adiciona o usuário atual ao grupo docker (opcional)

    ```bash
    sudo usermod -aG docker $USER
    ```

5. Testa a instalação do Docker

    ```bash
    docker run hello-world
    ```

    ![Exemplo de Teste Docker Arch](imagens/teste-docker-arch.png)

### Windows

1. Baixe o Docker Desktop:

    [https://desktop.docker.com/](https://desktop.docker.com/)

2. Instale o Docker com suporte ao **WSL2** ativado.

3. Reinicie o computador.

    ![Exemplo Docker no Windows](imagens/docker-windows.png)

---

## 🐳 Containers Isolados

### Subir containers básicos

```bash
docker run -d --name meu_container nginx:latest
docker run -it --rm ubuntu bash  # Modo interativo
```

![Exemplo de Containers Básicos](imagens/containers-basicos.png)

### Exemplos úteis

```bash
# Com mapeamento de portas
docker run -d -p 8080:80 --name web nginx

# Com volume persistente
docker run -d -v /dados:/app/data --name app meu_app

# Com variáveis de ambiente
docker run -d -e "MODO=prod" --name app_prod meu_app
```

![Exemplo de Containers Úteis](imagens/containers-uteis.png)

## 🎛 Docker Compose

### Arquivo básico (docker-compose.yml)

```yaml
version: '3'
services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
  db:
    image: postgres:13
    environment:
      POSTGRES_PASSWORD: senha123
```

### Comandos essenciais

```bash
# Sobe os serviços definidos no docker-compose.yml em background
docker-compose up -d

# Especifica um nome de projeto para a stack
docker-compose -p meu_projeto up

# Usa um arquivo docker-compose customizado (exemplo: pi-hole.yml)
docker-compose -f pi-hole.yml up -d

# Para e remove todos os serviços da stack
docker-compose down

# Mostra logs em tempo real de todos os serviços
docker-compose logs -f

# Mostra logs de um serviço específico
docker-compose logs -f nome_do_servico

# Reinicia um serviço específico
docker-compose restart nome_do_servico

# Verifica o status dos serviços
docker-compose ps

# Remove containers, redes e volumes órfãos
docker-compose down --volumes
```

## 🧹 Limpeza

### Remoção segura

```bash
docker container prune      # Containers parados
docker image prune          # Imagens não usadas
docker volume prune         # Volumes não usados
```

### Limpeza completa (cuidado!)

```bash
docker system prune -a --volumes  # Remove TUDO
docker rmi $(docker images -q)    # Todas imagens
docker rm -f $(docker ps -aq)     # Todos containers
```

## 🔍 Inspeção

### Logs

```bash
docker logs -f container_id   # Monitora logs em tempo real
docker logs --tail=100 container_id  # Últimas 100 linhas
```

### Redes

```bash
docker network ls             # Lista redes
docker network inspect rede   # Detalhes da rede
```

### Healthcheck

```bash
docker ps --format "table {{.Names}}\t{{.Status}}"  # Mostra status
docker inspect --format='{{json .State.Health}}' container_id  # Detalhes
```

## 🩺 Healthcheck (Exemplo)

No Dockerfile:

```dockerfile
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost/ || exit 1
```

No docker-compose.yml:

```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost"]
  interval: 30s
  timeout: 3s
  retries: 3
```

## ⚡ Dicas Rápidas

- `docker stats` - Monitora recursos em tempo real
- `docker exec -it container bash` - Acessa terminal interativo
- `docker save/load` - Exporta/importa imagens
- `docker cp` - Copia arquivos entre host/container
