# Curso de Docker - Youtube (Canal LINUXtips) - Descomplicando o Docker

### Aula 1
##### O que é um container?
Arquivo binário que contém tudo necessário para uma aplicação rodar. O container não possui um kernel próprio, então utiliza o kernel da máquina host. Em outras palavras, container é uma virtualização de uma aplicação (e não o sistema operacional como um todo).

Os containeres são gerenciados pela engine do docker, que roda em uma camada acima do sistema operacional.


### Aula 2
##### O que é o Docker?
Surgiu da empresa DotCloud, que era uma PaaS (como Heroku) com a ideia de rodar qualquer linguagem. A DotCloud subiu o projeto de containeres para o Github com código open source, tornando-se Docker. Grandes empresas como Google, Facebook, Spotify, Netflix e RedHat passaram a utilizar o Docker e torná-lo cada vez mais conhecido na comunidade.

### Aula 3
Uma imagem do Docker é composta por diversas camadas read-only. Apenas a última camada (mais acima) é read-write. Quando é preciso modificar um arquivo das camadas mais baixas, o Docker faz uma cópia desse arquivo e coloca na camada de read-write.

Além disso, a escrita é feita pela técnica copy-on-write. Ou seja, os arquivos originais são preservados e qualquer alteração é feita em uma cópia do arquivo.


### Aula 4
Docker utiliza alguns componentes do kernel Linux:

- Namespaces: permite fazer isolamento de componentes do SO. Principal responsável por fazer que o container tenha um ambiente isolado (cada container tem seu ip, mount point, etc)
  - PID namespace responsável por isolar os PIDs. Cada container tem sua própria árvore de Process Identificator. Os processos são isolados em cada container, mas também podem ser visualizados pelo host.
  - net namespace responsável por fazer o isolamento das interfaces de rede
  - mnt namespace faz o isolamento do file system. Cada file system de cada container é isolado e não compartilha com outros
  - ipc namespace responsável pelos semáforos e filas de mensagens padrão POSIX
  - uts namesapce responsável pelo isolamento de hostname do container, versão do SO, etc
  - user namespace responsável por fazer o isolamento do usuários no container

- Cgroups
  - isolamento e gerenciamento de recursos para o container. Tais recursos são CPU, memória, IO, etc

- Netfilter
  - módulo do iptables (para roteamento, redirecionamento de portas, etc)


### Aula 5
Instalação do Docker

- Só roda em processador 64b
- Versão do kernel: `uname - r`
- Versão do docker: `docker --version`


### Aula 6
- Comando `docker` é o CLI usado para interagir com o Docker Daemon
- `docker run image-name` (ex.: `docker run ubuntu`) é utilizado para executar um container. Se a imagem não existir na máquina, o Docker faz o download dela diretamente do Docker Hub
- `docker ps` mostra todos os containeres em execução
- `docker ps -a` mostra todos os containeres que já foram executados na máquina
- `docker images` mostra todas as imagens que estão na máquina
- `docker run -it image-name command` (ex.: `docker run -it ubuntu bash`) abre um terminal interativo dentro do container
- `docker run -d image-name` executa o container como um daemon, um processo em background
- Em sessões interativas no container, digite `ctrl+d` para finalizá-lo. Se quiser apenas sair, mas deixar o container rodando, utilize `ctrl+p+q`. Se quiser voltar para o container que está rodando, utilize `docker attach container-id (ou container-name)`
- `docker create image-name` cria, mas não executa um container
- `docker stop container-id (ou container-name)` para um container em execução
- `docker start container-id (ou container-nane)` executa um container parado
- `docker pause container-id (ou container-name)` pausa um container em execução
- `docker unpause container-id (ou container-name)` despaus um container em pausado
- `docker stats container-id (ou container-name)` mostra quanto de recurso um container está usando (cpu, memória, IO, processos)
- `docker top container-id (ou container-name)` mostra os processos rodando no container
- `docker logs container-id (ou container-name)` mostra os logs gerados pelo container
- `docker rm container-id (ou container-name)` remove um container que está parado
- `docker rm -f container-id (ou container-name)` remove um container que está rodando


### Aula 7
Por padrão, o Docker não tem limites para uso de memória e cpu. Quando há muitos containeres rodando sem gerenciamento de memória e cpu, pode haver problemas de performance neles.

`docker inspect container-id (ou container-name)` mostra diversas informações sobre recursos utilizados pelo container, como memória, cpu, portas, ip, etc.

`docker run --name test debian` é o comando que executa um container com um nome específico.

##### Memória
`docker inspect container-id (ou container-name) | grep -i mem` traz as informações de memória do container. O valor `Memory` indica a quantidade de memória, em MB, utilizada pelo container. Quando é 0, quer dizer que não há limites para o uso de memória. Para criar um container limitando a memória em 512MB, é preciso rodar o comando `docker run -it --memory (ou -m) 512m --name test debian`. Para alterar a quantidade de memória em um container que já está rodando, é preciso utilizar o comando `docker update --memory (ou -m) 1024m container-id (ou container-name)`.

##### CPU
Para limitar a utilização de cpu dos containeres, é preciso fazer o cálculo pela proporção. Supondo que haja 3 containeres rodando: o primeiro tem o limite de cpu de 1024, o segundo de 512 e o terceiro de 512 também. Neste caso, o container 1 pode usar 50% dos recursos de cpu, e os containeres 2 e 3 podem usar 25% dos recursos de cpu cada um.

`docker run -it --cpu-shares 1024 --name test debian` cria o container com os recursos de cpu limitados. Para checar utilize `docker inspect test | grep -i cpu`.

Para criar o cenário acima, utilize `docker run -it --cpu-shares 1024 --name container1 debian`, `docker run -it --cpu-shares 512 --name container2 debian` e `docker run -it --cpu-shares 512 --name container3 debian`. E para checar o que foi criado, `docker inspect container1 container2 container3 | grep -i cpushares`.

Para alterar o cpu de containeres já rodando, utilize `docker update --cpu-shares 256 container-id (ou container-name)`.


### Aula 8
##### Volumes
No Docker, volume é uma forma de colocar um file system (um diretório) dentro do container. Este file system está montado no container, mas não faz parte dele de fato. Em outras palavras, é um diretório de compartilhamento entre o host e o container.

Caso o container seja movido de máquina, o volume não vai junto, pois está persistido no host. O mesmo acontece quando um container termina sua execução. Tudo o que for alterado no diretório dentro do container, também será refletido no host.

Para criar um container especificando um volume, utilize `docker run -it -v /volume (nome do diretório) ubuntu bash`. Dentro do container, utilize o comando `df -h` para listar as partições. Para saber onde o volume está persistido no host, utilize `docker inspect -f {{.Mounts}} container-id (ou container-name)`.

Para mapear um diretório específico do host em um volume do container, crie um diretório no host com `mkdir /root/primeiro_dockerfile`. Crie um container apontando para o diretório criado com `docker run -it -v /root/primeiro_dockerfile:/volume (nome do volume) debian`. Isso faz com que o diretório `/root/primeiro_dockerfile` seja montado no container em `/volumes`.

##### Containeres data-only
Containeres data only que não precisam estar em execução, mas eles possuem volumes que são compartilhados com outros containeres. Para criar um container data-only `docker create -v /data --name dbdados debian`

O parâmetro `--volumes-from` importa volumes de outros containeres para utilizá-los em seu próprio container. Como exemplo, crie dois containeres Postgresql que utilizarão o volume `dbdados`.

```
docker run -p 5432:5432 --name pgsql1 --volumes-from dbdados -e POSTGRESQL_USER=docker -e POSTGRESQL_PASS=docker -e POSTGRESQL_DB=docker kamui/postgresql

docker run -p 5432:5432 --name pgsql2 --volumes-from dbdados -e POSTGRESQL_USER=docker -e POSTGRESQL_PASS=docker -e POSTGRESQL_DB=docker kamui/postgresql
```

- O parâmetro `-p` expõe uma porta do container ou faz bind de uma porta do container para uma porta do host (host:container).
- O parêmetro `-e` são para variáveis de ambiente. São variáves de ambientes passadas para o container.

Ao executar esses dois containeres do Postgres, o diretório dbdados é preenchido com diversos arquivos criados pelo servidor de banco de dados.


### Aula 9
Dockerfile é um arquivo análogo a um Makefile, mas serve para instruções de containeres.

Instruções de um Dockerfile:
- `FROM debian` primeira instrução do Dockerfile. Determina qual a imagem que será usada como base para sua nova imagem.
- `ADD path_from_host path_from_container` copia um arquivo do host para o container (inclusive arquivos empacotados, como .tar)
- `LABEL Description="foo"` serve para colocar metadata, como versão, fabricante, descrição, etc
- `COPY path_from_host path_from_container` copia arquivos ou diretórios do host para o container
- `ENTRYPOINT ["/usr/bin/apache2ctl", "-D", "FOREGROUND"]` permite que um binário executável seja o principal processo dentro do container. Se esse processo for finalizado, o container também é.
- `CMD ["sh", "-c", "echo", "$HOME"]` é um parâmetro do comando ENTRYPOINT do container
- `ENV myname="Vitor"` permite criar uma variável de ambiente no container
- `EXPOSE 80` mostra qual a porta do container será exposta
- `RUN apt-get update && apt-get install apache2 && apt-get clean` permite rodar comandos dentro do container. Quanto menos comandos `RUN` seu container tiver, melhor, pois cada `RUN` cria uma nova camada. É preferível concatenar diversos comandos em um `RUN`.
- `USER vitor` define um usuário padrão para a imagem. Caso essa instrução não seja passada, o usuário será `root`
- `WORKDIR /foo` define o diretório raiz do container. Quando entrar no container, esse será o diretório corrente
- `VOLUME /path_to_directory` define o volume do container
- `MAINTAINER Vitor Kusiaki` define a pessoa que mantém esse Dockerfile


### Aula 10
Com o comando `docker build` é possível construir uma imagem a partir de um Dockerfile. O comando é `docker build -t image_name:version path_to_dockerfile`.

##### Diferenças entre as instruções RUN, CMD e ENTRYPOINT:

`RUN` executa comandos, faz o commit e cria uma nova camada acima da camada que está com instruções executadas.

`ENTRYPOINT` permite um comando padrão que sempre será executado quando o container for executado.

`CMD` pode servir para colocar mais parâmetros para a instrução ENTRYPOINT, mas também pode servir para executar comandos que podem ser sobrescritos ao rodar um container.


### Aula 11
Para criar uma imagem a partir de um arquivo, o nome dele deve sempre ser `Dockerfile`. A primeira linha do arquivo deve ser sempre o `FROM`
Exemplo de instalação de um servidor apache:

```
FROM debian

RUN apt-get update && apt-get install -y apache2 && apt-get clean

ENV APACHE_LOCK_DIR="/var/lock"
ENV APACHE_PID_FILE="/var/run/apache2.pid"
ENV APACHE_RUN_USER="www-data"
ENV APACHE_RUN_GROUP="www-data"

LABEL Description="Webserver"

VOLUME /var/www/html

EXPOSE 80

ENTRYPOINT apachectl -D FOREGROUND

```

Para fazer o build da imagem, utilize `docker build -t image-name:version path_to_dockerfile`, no exemplo acima será `docker build -t webserver:1.0 .`

Para entrar no container, utilize `docker run -it webserver:1.0 bash`. Em seguida, pegue o IP do seu container com o comando `ip addr`, saia do container sem finalizá-lo com o comando `ctrl+p+q` e teste seu servidor apache utilizando o cURL `curl <container_ip>:80`


### Aula 12
[Docker Hub](https://hub.docker.com/) é um repositório público e privado mantido pelo Docker. É utilizado para compartilhamento de imagens. O componente do Docker responsável por armazenar e compartilhar imagens chama-se `registry` (ou `distribution`). O Docker Hub é um registry.

É possível fazer auditorias em imagens desconhecidas (e não confiáveis) utilizando o comando `docker inspect image-name:version`. Outro comando é o `docker history image-name:version` que permite visualizar as camadas da imagem.

Preparando uma imagem para o Docker Hub:

- O nome da imagem deve conter também o nome do usuário Docker Hub
- Para renomear uma imagem, utilize `docker tag image-id new-name`. Exemplo: `docker tag 5a17715803a9 vkusiaki/webserver:1.0`
- Para subir a imagem para o Docker Hub, faça o login com `docker login` e, em seguida, `docker push user-name/image-name:version`. Exemplo: `docker push vkusiaki/webserver:1.0`

Para baixar uma imagem do Docker Hub:
  - Na linha de comando, para procurar por imagens, utilize `docker search user-name(ou image-name)`. Exemplo: `docker search vkusiaki`
  - Utilize o comando `docker pull user-name/image-name` para fazer o download da imagem


### Aula 13
Um container será usado para montar um `registry` local. Utilizar o comando `docker run -d -p 5000:5000 --restart=always --name=registry registry:2`. O parâmetro `-p` indica a especificação de uma porta do host para uma porta do container. Para o registry, a porta 5000 será usada. O parâmetro `--restart=always` faz com que o host Docker reinicie o container automaticamente caso haja algum problema com ele.

Para fazer o push de uma imagem ao registry local, é preciso refazer a `tag` da imagem, utilizando o endereço do registry (no caso, localhost:5000). Para isso, utilize o comando `docker tag image-id localhost:5000/image-name`. O push deve ser feito com o comando `docker push image-tag`, exemplo: `docker push localhost:5000/webserver:1.0`. O `pull` da imagem é análogo ao `push`. Utilize `docker pull image-tag`, exemplo: `docker pull localhost:5000/webserver:1.0`.

Para listar todas as imagens que estão neste registry local, utilize um `cURL`. O comando é `curl localhost:5000/v2/_catalog`.