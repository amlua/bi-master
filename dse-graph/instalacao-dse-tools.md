Licença de uso dos produtos DataStax: Limited No-Fee License for a period of ninety (90) days solely for non-production purposes [End User License Agreement (EULA)](https://www.datastax.com/legal/datastax-enterprise-terms)

# Instalação Datastax Enterprise (DSE)
Referência: [Installing DataStax Enterprise 6.8 on a single node](https://docs.datastax.com/en/install/6.8/install/dseBasicInstall.html)

# Instalação JDK
sudo apt-get update
sudo apt-get install openjdk-8-jdk
java -version

# Instalação pacote libaio
sudo apt-get install libaio1

# Download e Instalação Datastax Enterprise
cd ~
curl -L https://downloads.datastax.com/enterprise/dse.tar.gz | tar xz

DSE version: Latest 6.8 patch: 6.8.10

# Criação de diretórios para os dados e o log do Datastax Enterprise
sudo mkdir /var/lib/cassandra
sudo mkdir /var/log/cassandra
sudo chown -R $USER:$GROUP /var/lib/cassandra
sudo chown -R $USER:$GROUP /var/log/cassandra

# Inicialização do banco DSE Graph
cd ~/dse-6.8.10/
vi ./resources/dse/conf/dse.yaml
./bin/dse cassandra -g -s

# Instalação do Datastax Studio
Referência: [Installing DataStax Studio 6.8](https://docs.datastax.com/en/install/6.8/install/installStudio.html)

cd ~
tar zvxf datastax-studio-6.8.9.tar.gz
cd ~/datastax-studio-6.8.9/
vi ./conf/configuration.yaml

# Inicialização do Datastax Studio
cd ~/datastax-studio-6.8.9/
./bin/server.sh &

# Instalação do Datastax Bulk Loader
Referência: [Installing DataStax Bulk Loader for Apache Cassandra](https://docs.datastax.com/en/dsbulk/doc/dsbulk/install/dsbulkInstall.html)

cd ~
curl -OL https://downloads.datastax.com/dsbulk/dsbulk-1.8.0.tar.gz 
tar zvxf dsbulk-1.8.0.tar.gz

