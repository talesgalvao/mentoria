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


docker ps                                       pega todos os containers em execução

docker ps -a                                    pega todos os containers existentes no host

ctrl + p + q                                    sai do container sem finalizar

docker attach (container_id [em execução])      acessa um container em execução

docker create                                   cria um container, mas não executa

docker start                                    starta um container

docker pause                                    pausa um container

docker unpause                                  tira o container do pause

docker stats (container_id)                     exibe estatísticas do container

docker top (container_id)                       exibe a lista de processos em execução no container

docker logs (container_id)                      exibe os logs do container

docker rm (container_id)                        remove o container do host

docker rm -f (container_id)                     força a remoção de um container do host

docker images                                   exibe todas as imagens

docker inspect (nome do container)              exibe as configs do container, como limite de
                                                memória, cpu, etc.

docker update (nome do container)

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


