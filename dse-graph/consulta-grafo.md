# Consultas ao Grafo __carteira_fundos__

Para ser executado em uma console Gremlin (por exemplo, em células de um [notebook Datastax Studio](BI-Master-consulta-grafo.studio-nb.tar)).

Referências: 

[Getting Started](https://tinkerpop.apache.org/docs/3.4.10/tutorials/getting-started/)

[Gremlin’s Anatomy](https://tinkerpop.apache.org/docs/current/tutorials/gremlins-anatomy/)

[Recipes](https://tinkerpop.apache.org/docs/current/recipes/#_recipes)

[PRACTICAL GREMLIN: An Apache TinkerPop Tutorial](http://kelvinlawrence.net/book/Gremlin-Graph-Guide.html)

## 1. Dados cadastrais e patrimônio líquido do fundo
```
g.V()
    .hasLabel('fundo')
    .has('cnpj',cnpj_fundo)
```
[Resultado](resultados-consulta/1-vertice-fundo.png)

```
g.V()
    .hasLabel('fundo')
    .has('cnpj',cnpj_fundo)
    .project('cnpj','nome','tipo','cotas','exclusivo','situacao','patrimonio_liquido')
    .by('cnpj')
    .by('nome')
    .by('tipo')
    .by(coalesce(values('cotas'), constant('-')))
    .by(coalesce(values('exclusivo'), constant('-')))
    .by(coalesce(values('situacao'), constant('-')))
    .by(coalesce(values('patrimonio_liquido'), constant('-')))
```

[Resultado](resultados-consulta/1-propriedades-fundo.json)

## 2. Gestores do fundo
