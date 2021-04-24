# Configuração e carga do banco de dados de grafos

* [Roteiro de instalação e configuração das ferramentas Datastax Enterprise](instalacao-dse-tools.md)
* [Roteiro de criação do grafo __carteira_fundos__](criacao-grafo-esquema.md)
* [Roteiro para carga de dados no grafo __carteira_fundos__](carga-dados.md)

# Análise e consultas ao grafo

* [Análise exploratória do grafo __carteira_fundos__](analise-grafo.md)
* [Consultas ao grafo __carteira_fundos__](consulta-grafo.md)
 
## Referências:
  * [Getting Started](https://tinkerpop.apache.org/docs/3.4.10/tutorials/getting-started/)
  * [Gremlin’s Anatomy](https://tinkerpop.apache.org/docs/current/tutorials/gremlins-anatomy/)
  * [Recipes](https://tinkerpop.apache.org/docs/current/recipes/#_recipes)
  * [PRACTICAL GREMLIN: An Apache TinkerPop Tutorial](http://kelvinlawrence.net/book/Gremlin-Graph-Guide.html)

## Laboratório:
  * [Imagem docker com o banco DSE Graph e o grafo carteira_fundos](https://hub.docker.com/amlua/bi-master)

Roteiro para executar a imagem:
```
docker run -it -p 9091:9091 amlua/bi-master
/root/start_dse_graph.sh
/root/start_datastax_studio.sh
```

Após a inicialização dos serviços, o Datastax Studio estará acessível na máquina executando o container, no endereço http://localhost:9091/
