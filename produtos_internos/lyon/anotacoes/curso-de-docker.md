#Docker

## O que é um container?


container tem uma arvore de processos separada, mas pode ser consultada na tabela de processos do host (ps -ef)

## Docker

### Comandos Docker

docker run (container_name)                     usado para executar/startar um container

** parametros do docker run **

  -ti                                             starta o container com terminal em modo interativo

  -d                                              starta o container em background (sem interação)

  --name (nome temporário para o container)       atribui um nome ao container

  --memory (valor)                                limita a utilização de memória ao valor informado

  --cpu-shares (valor)                            limita a utilização de CPU ao valor informado
  
  -v (nome do volume)                             cria um novo volume dentro do container

  --volumes-from (nome do container)              importa um volume de outro container
  
  -p (porta_host:porta_container)                 liga determinada porta do host a uma porta do
                                                  container

  -e (nome da env_var)                            define uma variável de ambiente
/
  --restart=(parametro)                           define quando o container pode ser restartado, o
                                                  parametro 'always' indica que sempre que parar 
                                                  um serviço o container pode ser restartado
                                                  automaticamente

docker ps                                       pega todos os containers em execução

docker ps -a                                    pega todos os containers existentes no host

ctrl + p + q                                    sai do container sem finalizar

docker attach (container_id [em execução])      acessa um container em execução

docker create                                   cria um container, mas não executa

docker start                                    starta um container

docker pause                                    pausa um container

docker unpause                                  tira o container do pause

docker history (nome_da_imagem)                 exibe o histórico de alterações da imagem

docker stats (container_id)                     exibe estatísticas do container

docker top (container_id)                       exibe a lista de processos em execução no container

docker logs (container_id)                      exibe os logs do container

docker tag (image_id) (novo_nome_da_imagem)     altera o nome da imagem

docker rm (container_id)                        remove o container do host

docker rm -f (container_id)                     força a remoção de um container do host

docker images                                   exibe todas as imagens

docker inspect (nome do container)              exibe as configs do container, como limite de
                                                memória, cpu, etc.

docker update (nome do container)

docker build                                    constrói a imagem com base no Dockerfile
  -t (nome_da_imagem):(versao_da_imagem) (dir)  sempre deve ser usado para fazer o build de uma
                                                imagem

### Limitando CPU e Memória dos containers

#### Memória

Se rodar o ```docker inspect {container_id}```, procurando por ```memory```, o terminal vai retornar
o limite de memória do container, caso retorne ```0``` significa que o container não tem limite de
memória. Para adicionar um limite:

##### ao iniciar um container

docker run -ti --memory 512m --name test-memory ubuntu

##### quando o container já está em execução

docker update -m 256m test-memory

#### CPU

O controle de uso da CPU pelos containers só pode ser informado por valor real, ex: 1024.

##### ao iniciar um container

docker run -ti --cpu-shares 1024 --name container-test ubuntu

##### quando o container já está em execução

docker update --cpu-shares 512 container-test

### Trabalhando com volumes

Volume é uma forma de colocar um diretório dentro do container, mas fora do filesystm do container.
Ele é montado no container, mas não faz parte dele.

Funciona como um compartilhamento entre o host e o container.

#### Adicionando um volume

docker run -ti -v /var/www/primeiro_dockerfile:/volume ubuntu

** estrutura **
  -v (nome do diretorio):(nome do volume) (nome do container)

Nesse caso estamos adicionando o diretório ```primeiro_dockerfile``` ao volume ```volume``` no
container ```ubuntu```.

Dentro do container eu acesso o ```primeiro_dockerfile``` dentro de ```/volume```, no meu host eu
encontro ele em ```/var/www```.


docker inspect -f {{.Mounts}} (container_id ou nome do container) - exibe as informações do volume
criado.

#### Compartilhando volumes entre containers

docker run --volumes-from (nome do container)

```
docker run -d -p 5432:5432 --name pgsql1 --volumes-from dbdados -e POSTGRESQL_USER=docker -e
POSTGRESQL_PASS=docker -e POSTGRESQL_DB=docker kamui/postgresql 
```
### Dockerhub
É um repositório privado/público, onde você pode disponibilizar/compartilhar a sua imagem. Empresas
e pessoas físicas podem publicar imagens e compartilhá-las no [docker hub](hub.docker.com).

#### Comandos Dockerhub

docker login                faz o login no dockerhub

docker search (image_name)  procura uma imagem no dockerhub

docker pull                 puxa uma imagem do dockerhub

docker push                 envia uma imagem para o dockerhub

#### Criando um Dockerhub local

É preciso criar um Registry local, Registry é responsável por armazenar as imagens, é como criar um
repositório de imagens local.


docker run -d -p 5000:5000 --restart=always --name registry registry:2

O Registry local não tem controle de usuário, por isso é importante que a imagem tenha:

(endereço_do_registry):(porta_do_registry)/(image_name)

Conforme o exemplo acima ficaria assim:

localhost:5000/webserver:1.0

* importante alterar o nome do Registry criado para que seja acessível através do nome, para isso:

docker tag f32a97de94e1 localhost:5000/webserver:1.0

O comando acima é usado para renomear uma imagem.


Para enviar uma imagem para o Registry local:

docker push localhost:5000/webserver:1.0

Os outros comandos do dockerhub também funcionam, só precisa colocar o endereço do registry após o
comando, como fizemos no docker push acima.

Para consultar as imagens disponíveis no Registry local é possível usar o comando:

curl localhost:5000/v2/_catalog

### Redes no Docker

##### Definir Dns
docker run -ti --dns 8.8.8.8 debian
indica o endereço do DNS que vai responder para o container

##### Definir hostname  
docker run -ti --hostname meupc debian
define um hostname dentro do container, é diferênte do parâmetro ```--name```, que define um nome
para a imagem, o hostname é o nome da máquina dentro do container.

##### Linkar containers
docker run -ti --link (container_name)

ex:

docker run -ti --name container1 debian

docker run -ti --link container1 --name container2 debian

isso vai deixar o container1 acessível ao container2.

##### Expor uma porta específica
docker run -ti --expose 80 debian

Roda a imagem debian, com a porta 80 acessível

##### Conectar uma porta do container a uma porta do host
docker run -ti --publish 8080:80 debian
Nesse caso a porta 80 do container está ligada a porta 8080 do meu host. Também funciona com o
```-p```

##### definir um mac address
docker run -ti --mac-address 12:34:de:b0:6b:61 debian

##### Usar interface de rede do host dentro do container
docker run -ti --net=host debian
Isso leva todas as informações de rede do host para dentro do container, inclusive rotas definidas
no /etc/hosts. Deixa o container acessível pelo endereço do host.

### Docker-Machine
Cria hosts para executar containers em lugares como aws, googlecloud, vms, etc.

#### Instalação

https://docs.docker.com/v17.09/machine/install-machine/

Também precisa instalar o VirtualBox: sudo apt install virtualbox

#### Criando host

docker-machine create --driver virtualbox mymachine


#### Acessando o Docker-Machine criado

```docker-machine env lmachine```
Para exibir as variáveis de ambiente da docker machine

```eval $(docker-machine env lmachine)```
Para exportar as variáveis de ambiente

Depois do ```eval``` você já está conectado na docker machine, se rodar ```docker ps``` ele não
retorna nenhum container em execução, isso porque a docker machine ainda não tem nada em execução.
O comando ```docker-machine ls``` exibe os processos que estão em execução nesse momento e só vai
retornar a docker machine porque só ela foi iniciada até esse momento.

#### Lista de comandos

```docker-machine ip (machine_name)
Exibe o ip da docker-machine

```docker-machine ssh (machine_name)
conecta no docker-machine via ssh

```docker-machine inspect (machine_name)```
Inspeciona os recursos utilizado pela Docker-Machine

docker-machine stop (machine_name)
Para a Docker-Machine

docker-machine ls (machine_name)
Exibe o status da Docker-Machine

docker-machine rm (machine_name)
Remove a Docker-Machine


### Docker Compose
Com o Docker Compose é possível criar um container com todas os componentes de uma aplicação, por
exemplo, banco + aplicação + redis. Sem o compose seria necessário criar 3 containers diferêntes.

#### Comandos

build             - indica o caminho do dockerfile
```build: .```

command           - envia um comando para ser executado dentro do container
```comand: bundle exec thin -p 3000```

container_name    - nome do container
```container_name: my-web-container```

dns               - indica o dns server
```dns: 8.8.8.8```

dns_seach         - especifica um search domain
```dns_search: example.com```

dockerfile        - especifica um Dockerfile alternativo
```dockerfile: Dockerfile-alternate```

env_file          - especifica um arquivo de variáveis de ambiente
```env_file: .env```

enviroment        - adiciona variáveis de ambiente
```enviroment: RACK_ENV: development```

expose            - expõe a porta do container
```
expose:
-3000
-8000
```
external_link     - linka containers que não foram especificados no docker-compose atual
```
external_links:
 - redis_1
 - project_db_1:mysql
```

extra_hosts - adiciona uma entrada no /etc/hosts do container
```
extra_hosts:
 - "somehost:162.242.195.82"
 - "otherhost:50.31.209.229"
```

image       - indica uma imagem que será usada na criação do container
```
image:ubuntu:16.04
```

labels      - adiciona metadata ao container
```
labels:
 com.example.description: "Accounting webapp"
 com.example.department: "Finance"
```

links       - linka containers dentro do mesmo docker-compose
```
links:
 - db
 -db:database
```

log_driver  - indica o formato de log a ser gerado (json-file, syslog, etc)
```
driver: syslog
```

logging     - similar ao log_driver, com opções
```
logging:
  driver: syslog
  options: 
    syslog-address: "tcp://192.168.0.42:123"
```

net         - modo de uso de rede
```
net: "bridge"
net: "host"
```

ports       - expõe as portas do container e do host
```
ports:
  - "3000"
  - "8000:8000" (redirect da porta do container com a porta do host)
```

volumes, volume_driver    - monta volumes no container
```
volumes:
  - /var/lib/mysql
  - /opt/data:/var/lib/mysql
  - ./cache:/tmp/cache
```

volumes_from        - monta volumes através de outro container
```
volumes_from
  - service_name
  - service_name:ro
```

