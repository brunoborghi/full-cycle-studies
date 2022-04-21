
# Docker

### Conceitos básicos

- Como funciona um container: Namespaces, Cgroups e Overlay File System (Camadas)
- Containers são imutáveis
- Overlay File System (Camadas)
- Quando container morre, tudo se perde
- Docker Client: executa os comandos
- Docker Host: Volumes, Cache, Network, Daemon API
- Docker Registry: repositório de imagens (Push e Pull)

### Arquitetura
![Image](https://docs.docker.com/engine/images/architecture.svg)


### Comandos e dicas sobre docker

Roda container com ubuntu, interativo ("não morre"), com tty (posso executar comandos) e inicializa o bash. Ao sair, --rm remove o container e não permite que seja adicionado ao histórico do Docker.

```docker
   docker run -it --rm ubuntu bash
```

Lista containers ativos

```docker
  docker ps
```

Lista containers ativos e inativos

```docker
  docker ps -a
```

Roda container com nginx com terminal independente do processo e refletido na porta 8080 do servidor que roda o client do docker (minha máquina)

```docker
  docker run -d -p 8080:80 nginx
```

Inicia container por id

```docker
  docker start [CONTAINER-ID]
```

Parar container por id

```docker
  docker stop [CONTAINER-ID]
```

Remove container por id

```docker
  docker rm [CONTAINER-ID]
```

Inicia container com nome específico

```docker
  docker run -d --name nginx nginx
```

Executa um comando dentro de um container (Ex: rodar o bash no container XPTO)

```docker
  docker exec [CONTAINER-NAME] [COMMAND]
```

Entrar em um container (Inverso do dittach, parecido com o docker exec)
```docker
  docker attach [CONTAINER-NAME]
```

### Volumes

Bind Mounts

```docker
  docker run -d --name nginx -p 8080:80 -v /home/bborghif/dev/studies/fullcycle/docker/html/:/usr/share/nginx/html nginx
```

Bind mounts explicito (mais atual)

```docker
  docker run -d --name nginx -p 8080:80 --mount type=bind,source="$(pwd)"/html/,target=/usr/share/nginx/html nginx
```

Lista volumes

```docker
  docker volume ls
```

### Images

Lista imagens

```docker
  docker images
```

Baixa imagem para ambiente local

```docker
  docker pull [IMAGE]
```

Remove uma imagem

```docker
  docker rmi [IMAGE]
```

### Dockerfile

A "receita do bolo"

```dockerfile
FROM nginx:latest

COPY html/ /usr/share/nginx/html

ENTRYPOINT [ "/docker-entrypoint.sh" ]
CMD [ "nginx", "-g", "daemon off;" ]
```
Construindo uma imagem do nginx com Vim através do Dockerfile

```docker
  docker build -t bborghif/nginx-com-vim:latest [DOCKERFILE-DIR] 
```

Dica: remover todos containers por lote

```docker
  docker rm $(docker ps -a -q) -f
```

### Docker Hub

Login no Docker Hub (username e password)

```docker
docker login
```

Logout no Docker Hub

```docker
docker logout
```

Enviar imagem para o repositorio

```docker
docker push [DOCKER-HUB-USERNAME]/[IMAGE-NAME]:[TAG]
```

### Networks

#### Tipos de rede

- Bridge - Comunicação entre containers
- Host - Mescla network do host (server docker) com a network do docker (containers)
- Overlay - Comunicação entre dockers que estão em servidores diferentes (hosts diferentes - Funciona como um cluster (Docker Swarm))
- MacVLAN - Comunicação através de Mac Address
- None - Sem rede, forma isolada


Lista redes
```docker
  docker network ls
```

Remove redes não utilizadas
```docker
  docker network prune
```

Inspeciona rede
```docker
  docker network inspect [NETWORK-NAME]
```

Criar uma rede
```docker
  docker network create --driver bridge mynetwork
```

Quando crio containers dentro da mesma rede de maneira explicita usando o parâmetro
```docker
  --network [NETWORK-NAME]
```
existe o mapeamento do IP por meio do nome do container (ping [CONTAINER-NAME] funciona)

###

Conecta o container à rede selecionada
```docker
  docker network connect [NETWORK-NAME] [CONTAINER-NAME]
```

Acessar porta do docker host
```docker
   http://host.docker.internal:[HOST-PORT-EXPOSED]
```
