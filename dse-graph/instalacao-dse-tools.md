# Roteiro de instalação e configuração das ferramentas Datastax Enterprise

Licença de uso dos produtos DataStax: [End User License Agreement (EULA)](https://www.datastax.com/legal/datastax-enterprise-terms) (Limited No-Fee License for a period of ninety (90) days solely for non-production purposes)

Servidor Ubuntu 20.04.2 LTS (64 bits)

## Datastax Enterprise (DSE) Graph - Banco de Dados de Grafos
Referência: [Installing DataStax Enterprise 6.8 on a single node](https://docs.datastax.com/en/install/6.8/install/dseBasicInstall.html)

DSE version: 6.8 patch: 6.8.10

### Instalação JDK
```sh
sudo apt-get update
sudo apt-get install openjdk-8-jdk
java -version
```

### Instalação pacote libaio
```sh
sudo apt-get install libaio1
```

### Download e Instalação Datastax Enterprise (DSE)
```sh
cd ~
curl -L https://downloads.datastax.com/enterprise/dse.tar.gz | tar xz
```

### Criação de diretórios para os dados e o log do DSE
```sh
sudo mkdir /var/lib/cassandra
sudo mkdir /var/log/cassandra
sudo chown -R $USER:$GROUP /var/lib/cassandra
sudo chown -R $USER:$GROUP /var/log/cassandra
```

### Configuração customizada

Alterar a diretiva ```realtime_evaluation_timeout_in_seconds``` no arquivo ```~/dse-6.8.10/resources/dse/conf/dse.yaml```:
```sh
realtime_evaluation_timeout_in_seconds: 300
```

### Inicialização do banco Datastax Enterprise, habilitando o DSE Graph e o DSE Search
Referência: [Starting DataStax Enterprise as a stand-alone process](https://docs.datastax.com/en/dse/6.0/dse-admin/datastax_enterprise/operations/startStop/startDseStandalone.html)

```sh
cd ~/dse-6.8.10/
./bin/dse cassandra -g -s
```

## Datastax Studio

### Instalação Datastax Studio
Referência: [Installing DataStax Studio 6.8](https://docs.datastax.com/en/install/6.8/install/installStudio.html)

```sh
cd ~
tar zvxf datastax-studio-6.8.9.tar.gz
cd ~/datastax-studio-6.8.9/
```

### Inicialização do Datastax Studio
Referência: [Starting and stopping DataStax Studio](https://docs.datastax.com/en/studio/6.8/studio/studioStartStop.html)

```sh
cd ~/datastax-studio-6.8.9/
./bin/server.sh &
```

## Datastax Bulk Loader

### Instalação do Datastax Bulk Loader
Referência: [Installing DataStax Bulk Loader for Apache Cassandra](https://docs.datastax.com/en/dsbulk/doc/dsbulk/install/dsbulkInstall.html)

```sh
cd ~
curl -OL https://downloads.datastax.com/dsbulk/dsbulk-1.8.0.tar.gz 
tar zvxf dsbulk-1.8.0.tar.gz
```