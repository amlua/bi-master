# Análise Exploratória do Grafo __carteira_fundos__

Para ser executado em uma console Gremlin (por exemplo, em células de um [notebook Datastax Studio](BI-Master-analise-grafo.studio-nb.tar)).

Referências: 
[Getting Started](https://tinkerpop.apache.org/docs/3.4.10/tutorials/getting-started/)
[Gremlin’s Anatomy](https://tinkerpop.apache.org/docs/current/tutorials/gremlins-anatomy/)
[Recipes](https://tinkerpop.apache.org/docs/current/recipes/#_recipes)
[PRACTICAL GREMLIN: An Apache TinkerPop Tutorial](http://kelvinlawrence.net/book/Gremlin-Graph-Guide.html)

## Cardinalidade

### 1.1. Número de vértices por tipo (label)
```
g.with("label-warning", false).V()
    .groupCount()
    .by(label) 
```
[Resultado](resultados-analise/1-1-numero-vertices-tipo.json)
