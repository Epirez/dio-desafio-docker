# Desafio projeto [DIO](https://www.dio.me/) utilizando Docker: implementando uma estrutura de Microsserviços :whale:

## Replicando na prática a tecnologia de containers ministrado pelo instrutor Denilson Bonatti, dentro do Bootcamp Linux do zero :penguin:
<p> <br>

Este projeto se baseia em uma situação hipotética em que  além do hipermercado que o dono tem, quer construir mais unidades na cidade, sua atual infraestrutura no hipermercado 1 onde responde as requisições feitas nos dispositivos dos caixas eletrônicos, porém deve ser tomar cuidado com o custo, equipamentos necessários para implementação dos demais data center pode ter altos custos. Então precisa-se analisar se o modelo atual é viável, poderia manter em um hipermercado um Data Center grande que consequentemente iria precisar de mais servidores, de uma VPN (link privado entre os hipermercados), entre outros. 

|**Problemas com a nuvem privada no data center local**| **Vantagens de migrar para a nuvem pública**                                    |
|-------------------------------------------------------|-----------------------------------------|
|- Dificuldade com a segurança e tecnologia da informação (lógica e física).| Preço (pague somente o que usar).|
|- Custo com a mão de obra especializada.|Facilidade de contratação, configuração e infraestrutura (vários players no mercado como: AWS, GCP, Microsoft Azure, Oracle…)|
|- Custo de Hardware.
|- Custo de Energia elétrica.| Escalabilidade (pode subir e descer várias máquinas facilmente).|
|- Falta de Energia com hipótese de uso de geradores.| Performance (Ainda mais se tiver uma boa internet).|
|- Despesas inesperada.|
<p><br>

#### Iniciando a VM via SSH :
![Criando Volumes](/Gif/1-Iniciando_Virtual%20Machine_via_SSH.gif)
```shell
ssh -i <chave ssh> <username>@<ip_público>
```

***

#### Instalando docker:
![Instalando docker](/Gif/2-Instalando_Docker.gif)

download do docker:
```shell
curl -fsSL [https://get.docker.com](https://get.docker.com/) -o get-docker.sh](http://get-docker.sh/)
```

 iniciando instalação:
```shell
sudo sh [get-docker.sh](http://get-docker.sh/)
```

 verificando se docker realmente está ativo:
```shell
systemctl status doker
```
***

#### Criando volumes:
![Criando volumes](/Gif/3-Criando%20volumes.gif)
A principal função do volume é persistir os dados. Diferentemente do filesystem do container, que é volátil e toda informação escrita nele é perdida quando o container morre, quando você escreve em um volume aquele dado continua lá, independentemente do estado do container.

```shell
docker volume create app
```

```shell
docker volume create data
```
***

####  Criar e Executar novo container:
![Criar e Executar novo container](/Gif/4%20-%20%20Criar%20e%20Executar%20novo%20contêiner.gif)

criar e executar novo container Docker com base em uma imagem especificada mysql:5.7:
```shell
docker run -e MYSQL_ROOT_PASSWORD=Senha123 -e MYSQL_DATABASE=meubanco --name mysql-A -d -p 3306:3306 --mount type=volume,src=data,dst=/var/lib/mysql/ mysql:5.7
```
<p><br>
<div style="background-color:yellow">
  <p style="color:blue">Detalhando o comando:</p>
</div>

|Partes do comando | o que faz                                                                        |
|--------|----------------------------------------------------------------------------------|
|`-e MYSQL_ROOT_PASSWORD=Senha123`| define a senha de root do MySQL para "Senha123" como uma variável de ambiente dentro do container |
|`-e MYSQL_DATABASE=meubanco`     | cria um banco de dados chamado "meubanco" como uma variável de ambiente dentro do container      |
|`--name mysql-A`                 | define o nome do container como "mysql-A"
|`-d`                             | executa o container em segundo plano (modo "detached")
|`-p 3306:3306`                   | mapeia a porta 3306 do container para a porta 3306 do host, permitindo que aplicativos externos se comuniquem com o MySQL dentro do container|
|`--mount type=volume,src=data,dst=/var/lib/mysql/`| cria um volume chamado "data" e o monta no diretório "/var/lib/mysql/" dentro do container, permitindo que os dados do banco de dados sejam armazenados persistentemente fora do container |
|`mysql:5.7`| especifica a imagem do Docker usada para criar o container, nesse caso, a imagem do MySQL versão 5.7.
<p><br>

listar os containers em execução no Docker host:
```shell
docker ps
```
***

#### Testando a conexão com banco de dados:
![Testando a conexão com banco de dados](/Gif/5%20-%20Teste%20conexão%20com%20banco%20de%20dados.gif)
Utilizadndo cliente SQL Sequeler para conectar ao banco de dados remoto.
***

#### Criando tabela no Sequeler:
![Criando tabela no sequeler](/Gif/6%20-%20Criando%20tabela%20no%20sequeler.gif)

```sql
CREATE TABLE dados (
    AlunoID int,
    Nome varchar(50),
    Sobrenome varchar(50),
    Endereco varchar(150),
    Cidade varchar(50),
    Host varchar(50)
);
```
*aqui poderia colocar os campos como se fossem uma tabela de producos de supermercado.

***

#### Exibindo a tabela:
![Exibindo a tabela](/Gif/7%20-%20Exibindo%20a%20tabela.gif)
```shell
  Select * FROM dados
```
***

#### Inserindo um index.php banco de dados receber informação:
![Criando um index.php para inserir informações do banco de dados](/Gif/8%20-%20Inserindo%20um%20index.php%20para%20inserir%20informações%20do%20banco%20de%20dados.gif)
Nesta etapa havia esquecido de alterar o IP para o do host atual, mas depois o fiz.

***

#### Crindo container para colocar arquivo index:
![Crindo container para colocar arquivo index](/Gif/9%20-%20Crindo%20container%20para%20colocar%20arquivo%20index.gif)
Precisa de um servidor apache para que o código php rode o do php instalado, necessário criar um container e inserir o arquivo index.php para dentro do container.
Depois de instalado terá um apache e php rodando sobre um Alpine do linux que também foi instalado, utilizando a porta padrão HTTP 80.

```shell
docker run --name web-server -dt -p 80:80 --mount type=volume,src=app,dst=/app/ webdevops/php-apache:alpine-php7
```
***

#### Realizando teste requisição com IP público:
![Realizando teste requisição com IP público](/Gif/10%20-%20Realizando%20teste%20requisição%20com%20IP%20público.gif)
Aqui realizo o teste de requisição com o ip púbico da vm servidor-1 criada na azure, e mostro a inserção feita no banco com o Sequeler, no final comparo ID (889b713...) do host da aplicação do container web-server que esta rodando com o campo host que esta na tabela. 
***

#### Adicionando mais 2 Virtual Machine:
![Adicionando mais 2 Virtual Machine](/Gif/11%20-%20Adicionando%20mais%202%20Virtual%20Machine.gif)
Os testes foram realizados na Azure, pore que já possuo conta de estudante e na AWS onde era pra ser feito o desafio, eu teria que fazer cadastro e adicionar um néumro de cartão de crédito, então achei mais prático usar da Azure.

***

### Estressando o container
Como o cenário atual vou realizar um teste estressando de requisições o um alvo, neste caso o servidor 1 com mysql e php, pode ser feito localmente com software específicos ou com sites que também fazem o teste, neste caso foi usados o [loader.io](https://loader.io/), onde o teste é limitado a um host por conta, a não ser que a conta for premium.

#### Gerando o arquivo para teste no servidor:
![Gerando o arquivo para teste no servidor](/Gif/12%20-%20Gerando%20arquivo%20para%20teste%20no%20servidor.gif)
Como no roteiro do desafio já sabia que seria necessário 3 servidores, os criei antes. Nesse inicio usa-se o ip público do servidor 1 para validar o teste no loader.io.

***

#### Preparando um arquivo para teste:
![Preparando um arquivo para teste](/Gif/13%20-%20Preparando%20um%20arquivo%20para%20teste.gif)
Nesta etapa copio o nome do arquivo gerado e crio cum arquivo txt dentro do container no caminho `/var/lib/docker/volumes/app/_data` que esta espelhado com o host do ip público que informei no site loader.io, como é pra teste coloco o nome do proprio arquivo de conteúdo,e no site loader.io como esta setado o host em que esta hospedado o container, realizo a verificação.

***

#### Testando com 50 requisições:
![Teste com 50 requisições](/Gif/14%20-%20Teste%20com%2050%20requisições.gif)
As configrações podem ser feito conforme necessidade, aqui apliquei a mesma configuração do istrutor pra tentar chegar em um resultado similar, porém tem muitas variantes, como conexão de internet, etc.

| Campo             | preenchido com                |
|-------------------|-------------------------------|
| Name              | Teste  Docker                 |
| Metod             | GET                           |
| Test type         | Client por test               |
| Protocol          | HTTP                          | 
| Clients           | 50                            |
| Host              | IP público do host            |
| Duration          | 1 minuto                      | 
| Path              | index.php                     |

***

### Iniciando um cluster Swarm
Na hipótese de não atender as necessidades da infraestrutura devido a testes de baixo desempenho. Então poderia multiplicar esse microsserviço em vários outros caontainers e servidores, tudo isso de maneira simples utilizando o Swarm do docker.

#### Matando processo container aplicão web-server php:
![Matando processo container aplicão web-server php](/Gif/15%20-%20Matando%20processo%20container%20aplicão%20web-server%20php.gif)
Matando o processo do primeiro container, por que esta isolado, vou colocar em 3 servidores e fazer outro teste

Verificando quais estão em execução:
```shell
docker ps
```
<p><br>
Este comando cria e executa um novo contêiner chamado "web-server" a partir da imagem "force" e o remove automaticamente após a sua execução ser finalizada:

```shell
docker --rm force web-server
```

#### Iniciando um cluster Swarm:
conO Swarm é uma ferramenta nativa do Docker para orquestração de contêineres, permitindo que você crie e gerencie vários contêineres em um cluster.

![Matando processo container aplicão web-server php](/Gif/16%20-%20Iniciando%20cluester%20swarm.gif)


Com este comando nó o atual é transformado em um nó gerente (manager node) do cluster Swarm, e é criado um token de segurança que permitirá que outros nós se juntem ao cluster, Uma vez que o nó atual é transformado em gerente do cluster, é possível criar serviços e tarefas, gerenciar nós, monitorar o cluster e escalonar aplicativos em um ambiente distribuído. Este comando é normalmente executado no nó que será o gerente inicial do cluster. Após a inicialização do cluster, outros nós podem ser adicionados usando o token de segurança gerado pelo comando "docker swarm init".


```shell
docker swarm init
```
Retorna o token:
![Token gerado pelo comando docker swarm init](/Gif/17%20-%20Token%20gerado%20pelo%20comando%20docker%20swarm%20init.png) 

***

#### Adicionando os outros servidores ao manager node:
![Inserindo token gerado no servidor 2 e 3](/Gif/18%20-%20Server%202%20e%203%20-%20adicionando%20ao%20cluster%20swarm%20.gif)
Nesta etapa instalo o Docker no servidor 2 e 3 e depois insiro tokern gerado, nele tem um IP de acesso público com porta 2377 da máquina virtual local, o Docker Swarm usa esta porta. Pensando em firewall tem que verificar no servidor se esta liberada pra poder funcionar.

A porta 2377 é usada para comunicação entre os gerenciadores do Swarm e para o gerenciamento do estado do cluster. É importante lembrar que as portas podem ter diferentes usos e finalidades em diferentes contextos, e é sempre recomendável verificar a documentação oficial para entender exatamente qual serviço ou aplicativo está usando uma determinada porta.

***

#### Verificando os nós pertencentes a esse cluster:
![](Gif/19%20-%20Server%201%20-%20Checado%20nós%20pertencente%20ao%20cluster.gif)
Um nó é uma instância de um host Docker que faz parte de um cluster Swarm e é capaz de executar serviços Docker. Esses nós podem ser adicionados ou removidos dinamicamente para alterar a escala ou a capacidade de um cluster.
<p><br>

Lista os nós (nodes) disponíveis em um cluster Docker Swarm:
```shell
docker node ls
```
***
#### Criando um novo serviço em um cluster Docker Swarm:
![Replicando serviço nos nós ativos do cluster](Gif/20%20-%20Replicando%20serviço%20nos%20nós%20ativos%20do%20cluster.gif)
Esse comando criará um serviço denominado "web-server" com três réplicas, mapeará a porta 80 do host para a porta 80 do contêiner, montará um volume chamado "app" no caminho "/app/" do container e usará a imagem "webdevops/php-apache:alpine-php7" como base para o container:


```shell
docker service create --name web-server --replicas 3 -dt -p 80:80 --mount type=volume,src=app,dst=/app/ webdevops/php-apache:alpine-php7
```
<p><br>
<p><br>
<div style="background-color:yellow">
  <p style="color:blue">Detalhando o comando:</p>
</div>

| Comando             |                           o que faz                           |
|---------------------|---------------------------------------------------------------|
|`--name web-server`  | define o nome do serviço como "web-server". Isso será usado para referenciar o serviço posteriormente.|
|`--replicas 3`       | especifica que o serviço deve ser escalado para três réplicas. Isso significa que três contêineres serão criados para executar esse serviço. |
| `-dt`               | indica que o serviço será executado em segundo plano (em modo detach) e que o terminal do host não deve estar vinculado ao contêiner em execução.|
| `-p 80:80`          |indica que a porta 80 do host deve ser mapeada para a porta 80 do contêiner. Isso permite que o serviço seja acessado por meio da porta 80 do host.|
| `--mount type=volume,src=app,dst=/app/` | define um volume montado dentro do contêiner. Isso é feito usando o tipo de volume "volume", com a origem "app" e o destino "/app/". Isso permitirá que dados sejam persistidos mesmo que o contêiner seja reiniciado.| 
|`webdevops/php-apache:alpine-php7` | especifica a imagem do contêiner que será usada para executar o serviço. Neste caso, o contêiner baseado em Alpine do PHP e Apache da webdevops será utilizado, com o PHP versão 7 instalado. |
<p><br>

Lista os contêineres em execução pertencentes ao serviço denominado "web-server":
```shell
docker service ps web-server
```
A saída deste comando contém informações importantes, como o número de réplicas que estão em execução, a imagem usada para criar os contêineres, o nó em que cada contêiner está sendo executado e o status atual de cada réplica (se está em execução, se falhou ou se foi removida). Essas informações são úteis para monitorar e solucionar problemas em serviços Docker em execução.

Os arquivos index.php e arquivo loaderio-axxxxx.txt criados anteriormente não replica automaticamente, então é necessário aplicar o NFS.
<p><br>

***
### Replicando um volume dentro do cluster com NFS
Uma replicação de volume dentro do cluster com NFS permite que os dados sejam compartilhados entre os contêineres em execução em diferentes nós em um cluster Docker Swarm. O Network File System (NFS) é um protocolo que permite compartilhar arquivos e diretórios entre diferentes computadores em uma rede.
Com essa configuração, cada contêiner que precisar do volume pode montá-lo como um sistema de arquivos compartilhado e acessar os dados contidos no volume. Além disso, quando um contêiner falha, outro contêiner pode assumir o seu lugar e continuar a acessar o volume sem interrupção.

Assim, a replicação de volume com NFS em um cluster Docker Swarm permite que os dados sejam compartilhados entre os contêineres em execução em diferentes nós, tornando o serviço mais escalável e resiliente. 

#### Instalando NFS server e common:
![Instalando NFS server e common](/Gif/21%20-%20Instalando%20NFS%20server%20e%20common.gif)
A instalação deve ser feira da seguinte forma

Primeiro servidor → deve se instalar o nfs-server onde esta a aplicação (microsserviço), os demais serão clientes desse primeiro servidor:
```shell
apt install nfs-server
```
<p><br>

Segundo e terçeiro servidor → instalar a biblioteca dos  aquivos necessários somente para o cliente:
```shell
apt isntall nfs-common
```
<p><br>

***
#### Criando o arquivo de configuração:
![Criando o arquivo de configuração](/Gif/22%20-%20Criando%20o%20arquivo%20de%20configura%C3%A7%C3%A3o.gif)
Aqui é configurado a replicação para os outros servidores, necessário informar qual o diretório, o ideal seria indicar o IP das máquinas que vou liberar, porém é apenas uma representação básica. Não é recomendável essa situação em ambiente de produção por afetar a segurança.

Editando o arquivo dentro do diretório "_data":
```shell
root@ubuntu-server-1:/var/lib/docker/volumes/app/_data# nano /etc/exports
```
<p><br>
Este linha deve ser incluida no conteudo do arquivo:

```shell
/var/lib/dpcker/volumes/app/_data *(rw,sync,subtree_chek)
``` 
| Comando          |  o que faz                                                                   |
|------------------|------------------------------------------------------------------------------|
|    rw            | indica que a partição pode ser montada para leitura e escrita.               |
|   sync           | indica que o NFS deve operar em modo síncrono, o que significa que os dados devem ser gravados no disco imediatamente após uma operação de gravação ser concluída. Isso pode ser útil em casos em que é necessária uma garantia de que os dados sejam gravados corretamente.   |
|  subtree_check   | abilita a verificação de subdiretórios. Isso significa que o NFS deve verificar se os diretórios pai e filho são compatíveis ao fazer uma operação em um diretório filho.|

***

### Exportando o diretório:
![Exportando o diretório](/Gif/23%20-%20Exportando%20o%20diretório%20e%20cheando%20o%20compartilhamento.gif)

Para que possa exportar o diretório, este comando não retorna nem uma mensagem:
 ```shell
exports -ar
```
<p><br>
Informa o que esta compartilhado:

 ```shell
showmount -e
```
***
#### Montando diretórios server 2 e 3:
![Montando diretórios server 2 e 3](/Gif/24%20-%20Montando%20diretórios%20no%20server%202%20e%203.gif)
Quando os contêineres são iniciados e os volumes Docker são criados, os dados serão armazenados no compartilhamento NFS remoto, que pode ser acessado por outros nós no cluster ou em outras máquinas na rede que tenham permissão para acessar o sistema de arquivos compartilhado via NFS. Isso é útil para criar volumes persistentes que podem ser acessados por vários contêineres em diferentes nós em um ambiente de cluster Docker Swarm, por exemplo.

```shell
mount -o v3 10.0.0.4:/var/lib/docker/volumes/app/_data /var/lib/docker/volumes/app/_data
```
***
#### Conferindo se foi montado:
![Conferindo se foi montado](/Gif//25%20-%20Conferindo%20se%20foi%20montado.gif)
Conferindo no diretório do comando abaixo:

```shell
ls var/lib/docker/volumes/app/_data/
```
 Se tivesse 16 máquinas ou mais teria que realizar essa operação de replicar o diretório, isso poderia ser feito com script.

***

### Criando um proxy utilizando o NGINX
O objetivo desse proxy é atuar como intermediário entre os clientes que solicitam recursos na Internet e os servidores de destino que fornecem esses recursos. 

O uso de um proxy permite que as solicitações dos clientes sejam roteadas por meio do contêiner NGINX, onde podem ser modificadas, filtradas ou encaminhadas para outros servidores. Isso pode ser útil em vários cenários, como balanceamento de carga, autenticação de usuários, redirecionamento de tráfego e isolamento de serviços.

Agora vou criar um proxy, quandro vir uma requisição do servidor 1, quero que essa requisição replique para todos os outros contêineres automaticamente, existem duas formas de fazer isso, pagando um palyer de serviços em nuvem para um serviço especifico pra isso ou usando um proxy interno no servidor 1.

#### 26 - Criando diretório e configurando nginx.conf:
![26 - Criando diretório e configurando nginx.conf.gif](Gif/26%20-%20Criando%20diretório%20e%20configurando%20nginx.conf.gif)

```shell
mkdir /proxy
```

```shell
cd /proxy
```

```shell
nano nginx.conf
```
Na configuração do arquivo nginx.conf coloquei `upstream ll` o correto é `upstream all`, no arquivo disponibilizado neste repositório esta correto, só alterar os IP conforme os do seus servidores remoto.

***
#### Criando arquivo chamado dockerfile:
![Criar arquivo chamado dockerfile](/Gif/27%20-%20Criar%20arquivo%20chamado%20dockerfile.gif)
FROM indica qual a imagem que vai usar, neste caso nginx, vai buscar no hub do docker o ngnix, depois que fazer o downlaod no computador ele vai pegar esse arquivo de configuração nginx.conf e vai mandar pra dentro do contêiner, dessa forma ele já vai configurando conforme os parâmetros indicados nessa configuração.:

```shell
nano dockerfile
```
É necessário subir um container com essa configuração que foi feita anteriormente, para isso utiliza-se docker build.

***

#### Usando bocker build para subir contêiner:
O Docker Build é usado para automatizar o processo de criação de imagens Docker a partir de um Dockerfile. Quando o Docker Build é executado, ele lê o Dockerfile, executa os comandos especificados no arquivo e gera uma imagem Docker que pode ser usada para criar contêineres. O Docker Build também verifica se todas as dependências necessárias estão presentes e, se não estiverem, as baixa automaticamente.

 Uma ferramenta útil para desenvolvedores e equipes de DevOps, pois permite que eles criem imagens Docker de forma automatizada e eficiente, o que simplifica o processo de implantação e gerenciamento de aplicativos em contêineres.

![Usando bocker build para subir contêiner](/Gif/28%20-%20Usando%20bocker%20build%20para%20subir%20contêiner.gif)
Construir uma nova imagem Docker a partir de um Dockerfile presente no diretório atual e nomeá-la com o nome "proxy-app".

```shell
docker build -t proxy-app
```
<p><br>

Detalhando o comando:
| Comando                          | o que faz                                               |
|----------------------------------|---------------------------------------------------------|
| `-t` ou `--tag`                  | é usada para nomear ou adicionar tags a uma imagem Docker. Neste caso, a tag "proxy-app" está sendo adicionada à imagem recém-construída.|
| `.` ponto final                  | especifica que o Dockerfile que será usado para construir a imagem está localizado no diretório atual. Isso significa que o Docker Build usará o Dockerfile presente neste diretório para construir a imagem.|

Então assim, ao executar o comando o Docker irá ler o Dockerfile no diretório atual, executar as instruções contidas nele e, em seguida, criar uma nova imagem Docker com a tag "proxy-app". Esta imagem poderá ser utilizada para criar novos contêineres a partir dela.
<p><br>

Listar todas as imagens Docker presentes no host Docker local:
```shell
docker image ls
```

***

#### Criando um novo contêiner a partir da imagem:
![cria um novo contêiner a partir da imagem](/Gif/29%20-%20Subir%20o%20contêiner%20a%20partir%20da%20imagem%20criada.gif)
Agora subir o docker run  para rodar o contêiner a partir da imagem que foi criada anteriormente. Permite que se conecte a imagem através de um terminal no host e acessar um serviço exposto na porta 4500 do contêiner. O contêiner será executado em segundo plano e terá um nome definido como "my-proxy-app".

```shell
docker run --name my-proxy-app -dti -p 4500:4500 proxy-app
```
Detalhando o comando:

| Comando                  | o que faz                                                            |
|--------------------------|----------------------------------------------------------------------|
| `--name my-proxy-app`    | define o nome do contêiner como "my-proxy-app". Isso permite referenciar o contêiner por este nome em vez do ID do contêiner.|
| `-d`                     | define o modo de execução como "detached" (desanexado), o que significa que o contêiner será executado em segundo plano. O terminal ficará livre para usar para outros comandos.|
| `-t`                     | aloca um pseudo-tty (terminal) dentro do contêiner, permitindo interação com o contêiner através do terminal do host.|
| `-i`                     | mantém a entrada padrão (stdin) aberta, permitindo que você insira comandos dentro do contêiner.|
| `-p 4500:4500`           | mapeia a porta 4500 do contêiner para a porta 4500 do host. Isso permite que o contêiner exponha um serviço através da porta 4500, que pode ser acessado a partir do host.|
| `proxy-app`              | o nome da imagem a ser usada para criar o contêiner.                 |



<p><br>
Lista todas as imagens Docker presentes no host Docker local:

```shell
docker container ls
```
***

### Estressando o cluster
Usado para avaliar a capacidade do cluster de lidar com uma carga de trabalho intensa e prolongada. O objetivo do teste é identificar possíveis pontos fracos no cluster e melhorar a sua capacidade de lidar com um grande número de solicitações.

*Desta etapa em diante não tem os gifs por que usei mais de um host pra fazer o primeiro teste no loader.io e é limitado 1 host por conta free, mas tem as capturas de tela com os testes que o instrutor Denilson Bonatti realizou.

Estressar o proxy criado pra verificar se ele realmente vai distribuir a carga para todo o meu cluster.

#### Usando novamente o loader.io  praa fazer teste:
![Usando novamente o loader.io  praa fazer test](/Gif/30%20-%20Usando%20novamente%20o%20loader.io%20%20praa%20fazer%20test.png)
O host selecionado em azul (34.201.104.100) mas a porta e 4500, então será necessário excluir este host.
<p><br>

![Usando novamente o loader.io  praa fazer test](/Gif/31%20-%20Criar%20novo%20host.png)
Depois de excluido, inseir novamente o host mas com a porta 4500, exemplo: 34.201.104.100:4500 e gerar novamente.
<p><br>

![Copiar nome para criar arquivo](/Gif/32%20-%20Copiar%20nome%20para%20criar%20arquivo.png)
Copiar os caracteres gerados para criar um novo arquivo.
<p><br>

![brindo no nano para editar e criar o arquivo](/Gif/33%20-%20Arquivo%20para%20novo%20teste.png)
Abrindo no nano para editar e criar o arquivo.
<p><br>

![Inserindo qulquer texto, apenas teste.](/Gif/34%20-%20Inserindo%20qulquer%20texto,%20apenas%20teste.png)
Inserindo qulquer texto, apenas teste.
<p><br>

![Verificando se o loader reconhece](/Gif/35%20-%20Verificando%20se%20o%20loader%20reconhece.png)
Verificando se o loader reconhece.
<p><br>

***
#### Iniciando teste:
![Clicar em novo teste](/Gif/36%20-%20Clicar%20em%20novo%20teste.png)
Clicar em Novo teste.
<p><br>

![Preencher as informações](/Gif/37%20-%20Preencher%20as%20informações.png)
Preencher com as informações semelhantes como das anteriores, depois clicar em Run Test.
<p><br>

![Gerando gráficos de testes](/Gif/37.1.%20-%20Gerando%20gráfico%20de%20testes.png)
Teste em andamento, respostas podem sofrer variações, devido por exemplo oscilações internet.
<p><br>

![Preencher as informações](/Gif/38%20-%20Sequeler.png)
Pra saber se realmente esta replicando, poderia pegar o loge de cada container e checar, mas no código foi colocado o host da pra saber de quem esta chegado, então se der um `SELECT * FROM dados` novamente, nas primeiras consultas viam do mesmo host, e na imagem no campo Host da pra observar que os hosts começaram a mudar.
Então com isso não estressa nem um servidor e também não estressa nem um container por que o docker cluster vai separar cada container existente, em cada momento é executado a aplicação e um host diminuindo assim a sobrecarga.
<p><br>




