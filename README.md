# Análise de Composição de Carteira de Fundos de Investimento
Trabalho Final de Conclusão do Curso BI-Master (PUC-Rio)

De acordo com dados da Associação Brasileira das Entidades dos Mercados Financeiro e de Capitais – ANBIMA, a indústria de fundos de investimento encerrou o ano de 2020 com captação líquida positiva, apesar de todo o impacto da crise finaneira causada pela pandemia do coronavírus (Referência: [Fundos encerram ano com captação líquida positiva de R$ 156,4 bilhões](https://www.anbima.com.br/pt_br/noticias/fundos-encerram-ano-com-captacao-liquida-positiva-de-r-156-4-bilhoes-8A2AB2B67692226E0176F83667DE2D01-00.htm)).

Tal fato ilustra a relevância do mercado de fundos de investimento brasileiro, evidenciada também pelo surgimento de diversas plataformas online de análise de fundos, tais como: [Mais Retorno](https://maisretorno.com/), [Info Fundos](https://infofundos.com.br/), [Comparador de Fundos](https://www.comparadordefundos.com.br/) e [Yubb](https://yubb.com.br/).
Essas plataformas disponibilizam informações tais como rentabilidade histórica, evolução do patrimônio, nível de risco e composição da careira, entre outras.

__O objetivo da presente prova de conceito é analisar a composição de carteira de fundos de investimento experimentando uma abordagem baseada em banco de dados de grafos (_graph database_).__ A composição da carteira de um fundo é o conjunto de ativos financeiros nos quais o patrimônio do fundo está aplicado. São exemplos de ativos financeiros: Letras Financeiras, debêntures, CDBs (Certificados de Depósito Bancário), ações, opções, contratos de Swap e cotas de outros fundos. A seleção dos ativos em que os recursos do fundo serão aplicados é de responsabilidade de seu(s) gestor(es). A escolha do modelo de dados baseado em grafos para representar este domínio de problema foi motivada pela necessidade de se produzir análises centradas nos relacionamentos entre as entidades: fundo _aplica_ em ativos; fundo _aplica_ em cotas de outros fundos; ativos _são emtidos_ por instituições financeiras/empresas; fundos _são geridos_ por pessoas físicas ou jurídicas.

![Modelo de Dados Composição de Carteira](https://github.com/amlua/bi-master/blob/main/GraphDataModel.png "Modelo de Dados Composição de Carteira")

A carteira dos fundos, a identificação de seus gestores e muitas outras informações relevantes são dados públicos que podem ser consultados, de forma centralizada, no [Portal de Dados Abertos da Comissão de Valores Mobiliários (CVM)](http://dados.cvm.gov.br/). Portanto, essa será a fonte principal de informações para a análise que se pretende.

__Importante: Os resultados da análise de carteiras de fundos apresentados nesse trabalho estão limitados pelo escopo da pesquisa acadêmica e não devem ser utilizados para classificar opções de investimento.__

## Ferramentas utilizadas na construção da prova de conceito:

* __[Linguagem Python](https://www.python.org/)__: obtenção, preparação e visualização dos dados
* __[Google Colab](https://colab.research.google.com/notebooks/intro.ipynb)__: ambiente de execução Python
* __[Datastax Enterprise (DSE) Graph](https://downloads.datastax.com/#enterprise)__: banco de dados de grafos
* __[DataStax Bulk Loader](https://downloads.datastax.com/#bulk-loader)__: ferramenta de carga de dados no banco de grafos
* __[Linguagem Gremlin](https://tinkerpop.apache.org/gremlin.html)__: consulta e travessia de grafos
* __[Datastax Studio](https://downloads.datastax.com/#studio)__: ambiente de execução Gremlin (análise exploratória de grafos)
