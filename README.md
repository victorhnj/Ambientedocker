# Ambientedocker
Ambiente Docker
## Configuração do Ambiente Docker Swarm com WordPress, MySQL, Prometheus, Redis e Grafana

Este guia oferece instruções passo a passo para configurar um ambiente web completo utilizando Docker Swarm. O ambiente inclui WordPress para gerenciamento de conteúdo, MySQL para banco de dados, Prometheus para monitoramento, Redis para cache e Grafana para visualização de dados.

## Pré-requisitos

- Ubuntu Server com Docker instalado
- Conhecimento básico de Docker

## Passo 1: Instalar Docker Compose

1. Instale o Docker Compose se ainda não estiver instalado:

   ```sh
   sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   sudo chmod +x /usr/local/bin/docker-compose


Verifique a instalação:

docker-compose --version

## Passo 2: Iniciar Docker Swarm

sudo docker swarm init


## Passo 3: Configurar a Rede Docker
1. Crie uma rede overlay para o Docker Swarm:

sudo docker network create --driver overlay my_network

## Passo 4: Criar os Arquivos de Configuração

1.Crie um diretório para os arquivos de configuração:
mkdir ~/docker-setup
cd ~/docker-setup


## Crie o arquivo docker-compose.yml: 

### nano docker-compose.yml

Informe esses Dados 

version: '3.7'

services:
  wordpress:
    image: wordpress:latest
    ports:
      - "80:80"
    environment:
      WORDPRESS_DB_HOST: mysql:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
    networks:
      - my_network

  mysql:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    volumes:
      - mysql_data:/var/lib/mysql
    networks:
      - my_network

  prometheus:
    image: prom/prometheus
    volumes:
      - prometheus_data:/prometheus
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"
    networks:
      - my_network

  redis:
    image: redis:latest
    ports:
      - "6379:6379"
    networks:
      - my_network

  grafana:
    image: grafana/grafana
    ports:
      - "3000:3000"
    environment:
      GF_SECURITY_ADMIN_USER: admin
      GF_SECURITY_ADMIN_PASSWORD: admin
    volumes:
      - grafana_data:/var/lib/grafana
    networks:
      - my_network

volumes:
  mysql_data:
  prometheus_data:
  grafana_data:

networks:
  my_network:
    external: true


 ## 3.Crie o arquivo prometheus.yml 
  ### nano prometheus.yml
Informe os seguinte dados 
 
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'wordpress'
    static_configs:
      - targets: ['wordpress:80']

  - job_name: 'mysql'
    static_configs:
      - targets: ['mysql:3306']



## Passo 5: Implantar o Stack no Docker Swarm
1.Implante o stack usando Docker Swarm:
sudo docker stack deploy -c docker-compose.yml my_stack

## Passo 6: Verificar e Acessar os Serviços
1.Verifique se os serviços estão em execução:
sudo docker stack services my_stack


## Acesse os serviços via navegador:

WordPress: http://<seu-ip>:80
Prometheus: http://<seu-ip>:9090
Grafana: http://<seu-ip>:3000

file:///home/victor/Imagens/Capturas%20de%20tela/Captura%20de%20tela%20de%202024-06-25%2021-26-53.png




## Passo a Passo para Corrigir a Conexão entre Redis e Docker
### 1. Acessar o Container do WordPress:
sudo docker ps

### 2. Acessar o Container do WordPress via Terminal:
sudo docker exec -it <ID_DO_SEU_CONTAINER_WORDPRESS> bash

### 3.0 Atualizar e Instalar o Nano (opcional, se necessário):
apt update
apt install nano

### 4. Editar o arquivo wp-config.php:
nano wp-config.php

### 5. Adicionar Configurações do Redis:
define('WP_CACHE', true);
define('WP_REDIS_HOST', 'redis');
define('WP_REDIS_PORT', 6379);

### 6. Salvar e Fechar o Arquivo:
Pressione CTRL+O para salvar as alterações.
Pressione Enter para confirmar o nome do arquivo.
Pressione CTRL+X para sair do nano.


## Verificar a Conexão com o Redis:
Após editar o arquivo wp-config.php, retorne ao painel do WordPress, atualize a página (pressionando F5) e verifique se o Redis agora aparece como "Acessível" nas configurações ou plugins do WordPress que estão utilizando o Redis.










