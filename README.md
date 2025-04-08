# NoSql---Cluster-MongoDB
Criação de um cluster para MongoDB com Docker

# **O que é um cluster?**
CLuster é um conjunto de servidores que trabalham em conjuto para fornecer mais escabilidade, 
desempenho e disponibilidade para algumas aplicações. Comumente usado em banco de dados e 
armazenamento de dados
# **Como o MongoDB trabalha?**
Replica Set (Alta Disponibilidade)
Um Replica Set é um grupo de servidores MongoDB que mantém cópias idênticas
dos dados para garantir tolerância a falhas. Ele é composto por:
Primária: O nó principal que recebe operações de escrita e leitura.
Secundárias: Nós secundários que replicam os dados do nó primário e
podem ser promovidos a primário caso ele falhe.
# *Arbiter* # (Opcional): Um nó que não armazena dados, mas participa das 
eleições para escolher um novo primário em caso de falha.


Comando para criar um cluster no Docker

`docker network create clusterDB`

Para criarmos o primeiro no iremos utilizar o seguinte comando.
`docker run -d --rm -p 27017:27017 --name mach1 --network clusterDB mongodb/mongodb-community-server:latest --replSet rs1 --bind_ip localhost,mach1`

Onde o comando **docker** run para a criação de um container o **-d** e usado para executar o container em segundo plano, o –rm é usado para caso o contêiner seja parado ele seja removido, o -p 27017:27017 usa a porta 27017 do host para a porta 27017 do MongoDB, o –name no1 nomeia o cluster com o nome no1, –network clusterDB, usado para conectar a rede criada anteriormente, mongodb/mongodb-community-server:latest usado para a imagem do container, –replSet replicaSet usado para dar nome ao replica Set como replicaSet, --bind_ip localhost,no1 diz ao MongoDB para aceitar as conexões do localhost.

Logo em seguida faremos os outros nós, aplicando o mesmo comando apenas alterando o nome dos nós e sus respectivas portas de conexão.

Nó 2 
`docker run -d --rm -p 27018:27017 --name mach2 --network clusterDB mongodb/mongodb-community-server:latest --replSet rs1 --bind_ip localhost,mach2`

Nó 3
`docker run -d --rm -p 27019:27017 --name mach3 --network clusterDB mongodb/mongodb-community-server:latest --replSet rs1 --bind_ip localhost,mach3`

Nó 4 
`docker run -d --rm -p 27020:27017 --name mach4 --network clusterDB mongodb/mongodb-community-server:latest --replSet rs1 --bind_ip localhost,mach4`

É interessante tambem adcinarmos um Nó arbitro, que será responsavel por fazer as escolhas de qual Nó se tornará o primario caso o primeiro Nó seja derrubado. Para isso utilizaremos o seguite comando.

`docker run -d --rm --name arbiter --network clusterDB mongodb/mongodb-community-server:latest --replSet rs1 --bind_ip localhost,arbiter`

Após os nós já terem sido adicionados iremos rodar o seguinte comando para acessar a ferramenta mongosh dentro do própio Docker.
`docker exec -it mach1 mongosh`
No terminal do Docker ele retornará uma url de conexão que usaremos futuramente no MongoDB.

##**mongodb://127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.4.2

Apos isso iremos realizar o seguinte comando abaixo para criar um replicaSet, com o seguinte comando rs.initiate:
`rs.initiate ({ _id: "rs1", members:[{_id:0, host: "mach1"}, {_id:1, host: "mach2"}, {_id:2, host: "mach3"}, {_id:3, host: "mach4"}, {_id:4, host:"arbiter", arbiterOnly: true}]})`

Podendo ja sair do mongosh
`exit`

# **Abrir MongoDB**
Com o MongoDB aberto iremos iniciar uma conexao copiando com aquela url que copiamos no Docker e colar na url de conexao do MongoDB.

`mongodb://127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.4.2`

Já conectados criaremos um banco de dados chamado alunos

`use alunos`

Ja podendo inserir alguns dados no banco no primeiro Nó com a porta 27017 com os seguintes comandos:

`db.matricula.insertOne({codigo:1, nome: "Luiz", telefone: "93939393"});`

`db.matricula.insertOne({codigo:1, nome: "Leonardo", telefone: "94567399"});`

Feito isso podemos começar a conectar os outros nós com portas 27018, 27019 e 27020. Lembrando que é só copiar a url e trocar a porta de conexão:
Nó2

#### mongodb://127.0.0.1:27018/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.4.2

Nó3

#### mongodb://127.0.0.1:27019/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.4.2

Nó4

#### mongodb://127.0.0.1:27020/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.4.2









