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

ker run -d --rm -p 27017:27017 --name nos1 --network clusterDB mongodb/mongodb-community-server:latest --replSet replicaSet --bind_ip localhost,nos1
