# Análise Exploratória do Grafo __carteira_fundos__

Para ser executado em uma console Gremlin (por exemplo, em células de um [notebook Datastax Studio](BI-Master-analise-grafo.studio-nb.tar)).

Posição da carteira dos fundos no grafo analisado: 11/2020.

Arquivo de dump do grafo analisado: [dse-unload-files.tar.gz](dse-unload-files.tar.gz).


## Cardinalidade

### 1.1. Número de vértices por tipo (_label_)
```
g.with("label-warning", false).V()
    .groupCount()
    .by(label) 
```
[Resultado](resultados-analise/1-1-numero-vertices-tipo.json)

### 1.2. Número de arestas por tipo (_label_)
```
g.with("label-warning", false).E()
    .groupCount()
    .by(label)
```
[Resultado](resultados-analise/1-2-numero-arestas-tipo.json)

## 2. Grau dos vértices
### Classificação dos vértices pelo número de arestas incidentes

### 2.1. Top 10 fundos com maior diversidade de ativos
#### 2.1.1. Top 10 fundos com maior diversidade de ativos (investidos diretamente)
```
g.V()
    .hasLabel('fundo')
    .project('cnpj', 'nome', 'degree')
    .by('cnpj')
    .by('nome')
    .by(outE('aplicacao_ativo').count())
    .order().by(select('degree'), desc)
    .limit(10)
```
[Resultado](resultados-analise/2-1-1-top-fundos-maior-diversidade-ativos.json)

#### 2.1.2. Top 10 fundos com maior diversidade de ativos (investidos direta e indiretamente)
Observação: A busca no grafo atravessa as arestas **aplicacao_fundo, tantas vezes quanto possível** (profundidade = n).
```
g.V()
    .hasLabel('fundo')
    .project('cnpj', 'nome', 'degree')
    .by('cnpj')
    .by('nome')
    .by(repeat(out('aplicacao_fundo').simplePath()).emit().dedup().outE('aplicacao_ativo').count())
    .order().by(select('degree'), desc)
    .limit(10)
```
[Resultado](resultados-analise/2-1-2-top-fundos-maior-diversidade-ativos.json)

### 2.2. Top 10 fundos com maior diversidade de ativos, dentre os que não investem em cotas de outros fundos
```
g.V()
    .hasLabel('fundo')
    .where(out('aplicacao_ativo'))
    .where(out('aplicacao_fundo').count().is(0))
    .project('cnpj', 'nome', 'degree')
    .by('cnpj')
    .by('nome')
    .by(outE('aplicacao_ativo').count())
    .order().by(select('degree'), desc)
    .limit(10)
```
[Resultado](resultados-analise/2-2-top-fundos-maior-diversidade-ativos.json)

### 2.3. Top 10 fundos com maior diversidade de ativos, dentre os que investem mais de 50% de seu patrimônio líquido em cotas de outros fundos
```
g.V()
    .hasLabel('fundo')
    .where(out('aplicacao_fundo'))
    .project('cnpj', 'nome', 'degree', 'pr_patrim_liq_cotas_fundos')
    .by('cnpj')
    .by('nome')
    .by(outE('aplicacao_ativo').count())
    .by(outE('aplicacao_fundo').values('pr_patrimonio_liquido').sum())
    .where(select('pr_patrim_liq_cotas_fundos').is(gt(50.0)))
    .order().by(select('degree'), desc)
    .limit(10)
```
[Resultado](resultados-analise/2-3-top-fundos-maior-diversidade-ativos.json)

### 2.4. Top 10 fundos com maior diversidade de fundos investidos diretamente
Observação: A busca no grafo atravessa a aresta **aplicacao_fundo apenas uma vez** (profundidade = 1).
```
g.V()
    .hasLabel('fundo')
    .where(out('aplicacao_fundo'))
    .project('cnpj', 'nome', 'degree', 'pr_patrim_liq_cotas_fundos')
    .by('cnpj')
    .by('nome')
    .by(outE('aplicacao_ativo').count())
    .by(outE('aplicacao_fundo').values('pr_patrimonio_liquido').sum())
    .where(select('pr_patrim_liq_cotas_fundos').is(gt(50.0)))
    .order().by(select('degree'), desc)
    .limit(10)
```
[Resultado](resultados-analise/2-4-top-fundos-maior-diversidade-fundos-investidos.json)

### 2.5. Top 10 fundos com maior diversidade de fundos investidos direta ou indiretamente
Observação: A busca no grafo atravessa a aresta **aplicacao_fundo n vezes** (profundidade = n).
```
g.V()
    .hasLabel('fundo')
    .project('cnpj', 'nome', 'degree')
    .by('cnpj')
    .by('nome')
    .by(repeat(out('aplicacao_fundo').simplePath()).emit().dedup().count())
    .order().by(select('degree'), desc)
    .limit(10)
```
[Resultado](resultados-analise/2-5-top-fundos-maior-diversidade-fundos-investidos.json)

### 2.6. Top 10 fundos com maior diversidade de fundos investidores diretos
Observação: A busca no grafo atravessa a aresta **aplicacao_fundo apenas uma vez** (profundidade = 1).
```
g.V()
    .hasLabel('fundo')
    .project('cnpj', 'nome', 'degree')
    .by('cnpj')
    .by('nome')
    .by(inE('aplicacao_fundo').count())
    .order().by(select('degree'), desc)
    .limit(10)
```
[Resultado](resultados-analise/2-6-top-fundos-maior-diversidade-fundos-investidores.json)

### 2.7. Top 10 fundos com maior diversidade de fundos investidores diretos ou indiretos
Observação: A busca no grafo atravessa a aresta **aplicacao_fundo n vezes** (profundidade = n).
```
g.V()
    .hasLabel('fundo')
    .project('cnpj', 'nome', 'degree')
    .by('cnpj')
    .by('nome')
    .by(repeat(__.in('aplicacao_fundo').simplePath()).emit().dedup().count())
    .order().by(select('degree'), desc)
    .limit(10)
```
[Resultado](resultados-analise/2-7-top-fundos-maior-diversidade-fundos-investidores.json)

### 2.8. Top 10 gestores com maior quantidade de fundos geridos
```
g.V()
    .hasLabel('gestor')
    .project('cpf_cnpj', 'nome', 'degree')
    .by('cpf_cnpj')
    .by('nome')
    .by(inE('gerido').count())
    .order().by(select('degree'), desc)
    .limit(10)
```
[Resultado](resultados-analise/2-8-top-gestores-mais-fundos-geridos.json)

### 2.9. Top 10 emissores com maior diversidade de ativos
```
g.V()
    .hasLabel('emissor')
    .project('id_emissor', 'nome', 'degree')
    .by('id_emissor')
    .by('nome')
    .by(inE('emitido').count())
    .order().by(select('degree'), desc)
    .limit(10)
```
[Resultado](resultados-analise/2-9-top-emissores-maior-diversidade-ativos.json)

### 2.10. Top 50 ativos com maior diversidade de fundos investidores diretos
Observações: 
1) O ranking considera somente os ativos com emissor identificado. 
2) A busca no grafo __não__ atravessa a aresta **aplicacao_fundo**.
```
g.V()
    .hasLabel('ativo')
    .where(out('emitido')) // Considera somente os ativos com emissor identificado
    .project('id_ativo', 'descricao', 'isin', 'ticker', 'emissor', 'ticker_desc', 'degree')
    .by('id_ativo')
    .by('descricao')
    .by(coalesce(values('isin'), constant('-')))
    .by(coalesce(values('ticker'), constant('-')))
    .by(out('emitido').values('nome')) // nome do emissor do ativo
    .by(values('ticker','descricao'))  // identificador alternativo para o ativo: ticker ou descrição
    .by(__.in('aplicacao_ativo').dedup().count()) // conta os vértices distintos, pois a cardinalidade da aresta é múltipla
    .order().by(select('degree'), desc)
    .limit(50)
```
[Resultado](resultados-analise/2-10-top-ativos-maior-diversidade-fundos-investidores.json)

### 2.11. Top 50 ativos com maior diversidade de fundos investidores diretos ou indiretos
Observações: 
1) O ranking considera somente os ativos com emissor identificado. 
2) A busca no grafo atravessa a aresta **aplicacao_fundo apenas uma vez** (profundidade = 1).
```
g.V()
    .hasLabel('ativo')
    .where(out('emitido')) // Considera somente os ativos com emissor identificado
    .project('id_ativo', 'descricao', 'isin', 'ticker', 'emissor', 'ticker_desc', 'degree')
    .by('id_ativo')
    .by('descricao')
    .by(coalesce(values('isin'), constant('-')))
    .by(coalesce(values('ticker'), constant('-')))
    .by(out('emitido').values('nome')) // nome do emissor do ativo
    .by(values('ticker','descricao'))  // identificador alternativo para o ativo: ticker ou descrição
    .by(union(
        __.in('aplicacao_ativo').dedup(),   // conta os vértices distintos, pois a cardinalidade da aresta é múltipla
        __.in('aplicacao_ativo').dedup()    // conta os vértices distintos, pois a cardinalidade da aresta é múltipla
            .repeat(__.in('aplicacao_fundo')).times(1).dedup()
    ).dedup().count())    
    .order().by(select('degree'), desc)
    .limit(50)
```
[Resultado](resultados-analise/2-11-top-ativos-maior-diversidade-fundos-investidores.json)

### 2.12. Top 10 ações com maior diversidade de fundos investidores diretos
Observações: 
1) O ranking considera somente os ativos com ticker e emissor identificado. 
2) A busca no grafo __não__ atravessa a aresta **aplicacao_fundo**.
```
g.V()
    .hasLabel('ativo')
    .has('descricao', prefix('ACOES'))
    .has('ticker')          // identificador do ativo na bolsa
    .where(out('emitido'))  // Considera somente os ativos com emissor identificado
    .project('id_ativo', 'descricao', 'ticker', 'emissor', 'degree')
    .by('id_ativo')
    .by('descricao')
    .by('ticker')
    .by(out('emitido').values('nome'))   // nome do emissor do ativo
    .by(__.in('aplicacao_ativo').dedup().count())  // conta os vértices distintos, pois a cardinalidade da aresta é múltipla
    .order().by(select('degree'), desc)
    .limit(10)
```
[Resultado](resultados-analise/2-12-top-acoes-maior-diversidade-fundos-investidores.json)

### 2.13. Top 10 ações com maior diversidade de fundos investidores diretos ou indiretos
Observações: 
1) O ranking considera somente os ativos com ticker e emissor identificado. 
2) A busca no grafo atravessa a aresta **aplicacao_fundo apenas uma vez** (profundidade = 1).
```
g.V()
    .hasLabel('ativo')
    .has('descricao', prefix('ACOES'))
    .has('ticker')          // identificador do ativo na bolsa
    .where(out('emitido'))  // Considera somente os ativos com emissor identificado
    .project('id_ativo', 'descricao', 'ticker', 'emissor', 'degree')
    .by('id_ativo')
    .by('descricao')
    .by('ticker')
    .by(out('emitido').values('nome')) // nome do emissor do ativo
    .by(union(
        __.in('aplicacao_ativo').dedup(),   // conta os vértices distintos, pois a cardinalidade da aresta é múltipla
        __.in('aplicacao_ativo').dedup()    // conta os vértices distintos, pois a cardinalidade da aresta é múltipla
            .repeat(__.in('aplicacao_fundo')).times(1).dedup()
    ).dedup().count())      
    .by(inE('aplicacao_ativo').count())
    .order().by(select('degree'), desc)
    .limit(10)
```
[Resultado](resultados-analise/2-13-top-acoes-maior-diversidade-fundos-investidores.json)

### 2.14. Top 10 emissores de ativos com maior diversidade de fundos investidores diretos
Observação: A busca no grafo __não__ atravessa a aresta **aplicacao_fundo**.
```
g.V()
    .hasLabel('emissor')
    .project('id_emissor', 'nome', 'degree')
    .by('id_emissor')
    .by('nome')
    .by(inE('emitido').outV().in('aplicacao_ativo').dedup().count())   // conta os vértices distintos, pois a cardinalidade da aresta é múltipla
    .order().by(select('degree'), desc)
    .limit(10)
```
[Resultado](resultados-analise/2-14-top-emissores-maior-diversidade-fundos-investidores.json)

### 2.15. Top 10 emissores de ativos com maior diversidade de fundos investidores diretos ou indiretos
Observação: A busca no grafo atravessa a aresta **aplicacao_fundo apenas uma vez** (profundidade = 1).
```
g.V()
    .hasLabel('emissor')
    .project('id_emissor', 'nome', 'degree')
    .by('id_emissor')
    .by('nome')
    .by(union(
        inE('emitido').outV().in('aplicacao_ativo').dedup(),   // conta os vértices distintos, pois a cardinalidade da aresta é múltipla
        inE('emitido').outV().in('aplicacao_ativo').dedup()    // conta os vértices distintos, pois a cardinalidade da aresta é múltipla
            .repeat(__.in('aplicacao_fundo')).times(1).dedup()
    ).dedup().count())      
    .order().by(select('degree'), desc)
    .limit(10)
```
[Resultado](resultados-analise/2-15-top-emissores-maior-diversidade-fundos-investidores.json)

## 3. Detecção de Ciclos
Busca no subgrafo G<sub>s</sub>(V<sub>s</sub>, A<sub>s</sub>) para verificar a existência de ciclos (caminhos fechados, onde os vértices de origem e destino coincidem).
V<sub>s</sub> = conjunto dos vértices do tipo __fundo__.
A<sub>s</sub> = conjunto das arestas do tipo __aplicacao_fundo__.
```
g.V()
    .hasLabel('fundo')
    .repeat(out('aplicacao_fundo')).until(cyclicPath())
    .path()
```
Resultado: ```No Data Returned```

Não foram encontrados ciclos no subgrafo.

