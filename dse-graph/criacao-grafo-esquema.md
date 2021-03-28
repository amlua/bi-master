# Roteiro de criação do grafo __carteira_fundos__

Para ser executado em uma console Gremlin (por exemplo, em células de um [notebook Datastax Studio](BI_Master_criacao_carga__dados_grafo_carteira_fundos.studio-nb.tar)).

Referência: [Notebook code editor in Gremlin code cells](https://docs.datastax.com/en/studio/6.0/studio/reference/gremlinCodeInNotebook.html)

## Criação do Grafo
Referência: [Examining graphs](https://docs.datastax.com/en/dse/6.0/dse-dev/datastax_enterprise/graph/using/examineGraph.html)

```
system.graph('carteira_fundos')
    .ifNotExists()
    .withReplication("{'class': 'org.apache.cassandra.locator.NetworkTopologyStrategy', 'SearchGraph': '1'}")
    .andDurableWrites(true)
    .create()  
```

## Criação do Esquema
Referência: [Managing graph schema](https://docs.datastax.com/en/dse/6.0/dse-dev/datastax_enterprise/graph/using/manageSchemaTOC.html)

### Vértices do Grafo

```
schema.vertexLabel('ativo')
	.ifNotExists()
	.partitionBy('id_ativo', Varchar)
	.property('data_vencimento', Date)
	.property('descricao', Varchar)
	.property('isin', Varchar)
	.property('mercado_negociacao', Varchar)
	.property('pais', Varchar)
	.property('ticker', Varchar)
	.create()
	
schema.vertexLabel('emissor')
	.ifNotExists()
	.partitionBy('id_emissor', Varchar)
	.property('cpf_cnpj', Varchar)
	.property('nome', Varchar)
	.create()
	
schema.vertexLabel('fundo')
	.ifNotExists()
	.partitionBy('cnpj', Varchar)
	.property('cotas', Boolean)
	.property('exclusivo', Boolean)
	.property('nome', Varchar)
	.property('patrimonio_liquido', Decimal)
	.property('situacao', Varchar)
	.property('tipo', Varchar)
	.create()
```

### Arestas do Grafo
	
```
schema.vertexLabel('gestor')
	.ifNotExists()
	.partitionBy('cpf_cnpj', Varchar)
	.property('nome', Varchar)
	.create()
	
schema.edgeLabel('emitido')
	.tableName('emitido')
	.ifNotExists()
	.from('ativo')
	.to('emissor')
	.partitionBy(OUT, 'id_ativo', 'ativo_id_ativo')
	.clusterBy(IN, 'id_emissor', 'emissor_id_emissor', Asc)
	.create()
	
schema.edgeLabel('aplicacao_ativo')
	.tableName('aplicacao_ativo')
	.ifNotExists()
	.from('fundo')
	.to('ativo')
	.partitionBy(OUT, 'cnpj', 'fundo_cnpj')
	.clusterBy(IN, 'id_ativo', 'ativo_id_ativo', Asc)
	.clusterBy('id_aplic', Int, Asc)
	.property('pr_patrimonio_liquido', Decimal)
	.property('valor', Decimal)
	.create()
	
schema.edgeLabel('aplicacao_fundo')
	.tableName('aplicacao_fundo')
	.ifNotExists()
	.from('fundo')
	.to('fundo')
	.partitionBy(OUT, 'cnpj', 'out_cnpj')
	.clusterBy(IN, 'cnpj', 'in_cnpj', Asc)
	.property('pr_patrimonio_liquido', Decimal)
	.property('valor', Decimal)
	.create()
	
schema.edgeLabel('gerido')
	.tableName('gerido')
	.ifNotExists()
	.from('fundo')
	.to('gestor')
	.partitionBy(OUT, 'cnpj', 'fundo_cnpj')
	.clusterBy(IN, 'cpf_cnpj', 'gestor_cpf_cnpj', Asc)
	.create()
```

### Índices e Views Materializadas 
Otimizam o desempenho das consultas e travessias no grafo. Cada um desses índices teve sua construção sugerida pelo Index Analyzer do DSE, durante a análise exploratória.

Referência: [Indexing](https://docs.datastax.com/en/dse/6.8/dse-dev/datastax_enterprise/graph/using/indexing.html)
	
```
schema.vertexLabel('ativo')
	.searchIndex()
	.ifNotExists()
	.by('id_ativo').asString()
	.by('descricao')
	.by('isin')
	.by('ticker')
	.create()

schema.vertexLabel('fundo')
	.searchIndex()
	.ifNotExists()
	.by('cnpj').asString()
	.by('patrimonio_liquido')
	.asString()
	.create()
	
schema.edgeLabel('emitido')
	.from('ativo')
	.to('emissor')
	.materializedView('emitido_by_emissor_id_emissor')
	.ifNotExists()
	.inverse()
	.create()
	
schema.edgeLabel('aplicacao_ativo')
	.from('fundo')
	.to('ativo')
	.materializedView('aplicacao_ativo_by_ativo_id_ativo')
	.ifNotExists()
	.partitionBy(IN, 'id_ativo')
	.clusterBy(OUT, 'cnpj', Asc)
	.clusterBy('id_aplic', Asc).create()
	
schema.edgeLabel('aplicacao_fundo')
	.from('fundo')
	.to('fundo')
	.materializedView('aplicacao_fundo_by_in_cnpj')
	.ifNotExists()
	.inverse()
	.create()
	
schema.edgeLabel('gerido')
	.from('fundo')
	.to('gestor')
	.materializedView('gerido_by_gestor_cpf_cnpj')
	.ifNotExists()
	.inverse()
	.create()
```
	
