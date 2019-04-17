FROM debian define qual é a imagem base da minha imagem

MAINTAINER Lyon lyoncesar@gmail.com

RUN apt-get update && apt-get install apache2 && apt-get clean  executa comandos no container,
importante evitar a criação de muitas camadas no container (cada vez que executo o RUN será gerada
uma nova camada no container).

ADD nome do arquivo (adiciona um arquivo ao container)

CMD ["sh", "-c","echo", "$HOME" ] é um parametro para o entrypoint

LABEL Description="descrição do container" também é usado para criar metadados no container

COPY faz uma cópia de um arquivo para dentro do container

ENTRYPOINT ["/usr/bin/apache2ctl", "-D", "FOREGROUND"] permite definir o principal processo dentro
do container (se esse processo morrer, o container também morre)

ENV meunome="Lyon Oliveira" definição de variaveis de ambiente para o container

EXPOSE 80 define a porta do container que será exposta

USER define qual será o usuário que vai executar o container

WORKDIR define o diretorio de trabalho do container

VOLUME /diretorio cria um volume no container
