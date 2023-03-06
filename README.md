# Desafio projeto [DIO](https://www.dio.me/) utilizando Docker: implementando uma estrutura de Microsserviços :whale:

## Replicando na prática a tecnologia de containers ministrado pelo instrutor Denilson Bonatti, dentro do Bootcamp Linux do zero :penguin:
<p> <br>


#### Iniciando a VM via SSH :
![Criando Volumes](/Gif/2-Instalando_Docker.gif)
```shell
ssh -i <chave ssh> <username>@<ip_público>
```

___

#### Instalando docker:
![Instalando docker](/Gif/2-Instalando_Docker.gif)

```shell
curl -fsSL [https://get.docker.com](https://get.docker.com/) -o get-docker.sh](http://get-docker.sh/)
```
Download do docker


`sudo sh [get-docker.sh](http://get-docker.sh/)` > iniciando instalação.

`systemctl status doker` > verificando se docker realmente está ativo.
___

#### Criando volumes:
![Criando volumes](/Gif/3-Criando%20volumes.gif)
A principal função do volume é persistir os dados. Diferentemente do filesystem do container, que é volátil e toda informação escrita nele é perdida quando o container morre, quando você escreve em um volume aquele dado continua lá, independentemente do estado do container.

`docker volume create app`

`docker volume create data`
___

####  Criar e Executar novo container:
![Criar e Executar novo container](/Gif/4%20-%20%20Criar%20e%20Executar%20novo%20contêiner.gif)

`docker run -e MYSQL_ROOT_PASSWORD=Senha123 -e MYSQL_DATABASE=meubanco --name mysql-A -d -p 3306:3306 --mount type=volume,src=data,dst=/var/lib/mysql/ mysql:5.7` > criar e executar novo container Docker com base em uma imagem especificada mysql:5.7

<div style="background-color:yellow">
  <p style="color:blue">Detalhando o comando</p>
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

`docker ps` > listar os containers em execução no momento no Docker host.
___

#### Testando a conexão com banco de dados:
![Testando a conexão com banco de dados](/Gif/5%20-%20Teste%20conexão%20com%20banco%20de%20dados.gif)
Utilizadndo cliente SQL Sequeler para conectar ao banco de dados remoto.
___

#### Criando tabela no sequele:
![Criando tabela no sequele](/Gif/6%20-%20Criando%20tabela%20no%20sequele.gif)
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

___
