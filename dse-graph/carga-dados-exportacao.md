# Roteiro para carga de dados no grafo carteira_fundos

Para ser executado no prompt de comando do servidor DSE Graph.

Os dados para carga estão no formato csv e são resultado da execução do [notebook Colab](../dados-abertos/download-preparacao.ipynb): [dse-load-files.tar.gz](dse-load-files.tar.gz).

```sh
cd ~
tar zvxf dse-load-files.tar.gz

cd ~/dsbulk-1.8.0/

./bin/dsbulk load -url ~/dse-load-files/gestores.csv -g carteira_fundos -v gestor -delim ',' -header true -m '0=cpf_cnpj, 1=nome'

./bin/dsbulk load -url ~/dse-load-files/fundos.csv -g carteira_fundos -v fundo -delim ',' -header true -m '0=tipo, 1=cnpj, 2=nome, 3=situacao, 5=cotas, 6=exclusivo, 7=patrimonio_liquido'

./bin/dsbulk load -url ~/dse-load-files/fundos_gestores.csv -g carteira_fundos -e gerido -delim ',' -header true -m '0=fundo_cnpj, 1=gestor_cpf_cnpj' -from fundo -to gestor

./bin/dsbulk load -url ~/dse-load-files/emissores.csv -g carteira_fundos -v emissor -delim ',' -header true -m '0=cpf_cnpj, 1=nome, 2=id_emissor'

./bin/dsbulk load -url ~/dse-load-files/ativos.csv -g carteira_fundos -v ativo -delim ',' -header true -m '0=id_ativo, 1=ticker, 2=isin, 3=descricao, 4=data_vencimento, 5=mercado_negociacao, 6=pais'

./bin/dsbulk load -url ~/dse-load-files/ativos_emissores.csv -g carteira_fundos -e emitido -delim ',' -header true -m '0=ativo_id_ativo, 1=emissor_id_emissor' -from ativo -to emissor

./bin/dsbulk load -url ~/dse-load-files/aplicacoes_ativos.csv -g carteira_fundos -e aplicacao_ativo -delim ',' -header true -m '0=fundo_cnpj, 1=valor, 2=pr_patrimonio_liquido, 3=ativo_id_ativo, 4=id_aplic' -from fundo -to ativo

./bin/dsbulk load -url ~/dse-load-files/aplicacoes_cotas_fundos.csv -g carteira_fundos -e aplicacao_fundo -delim ',' -header true -m '0=out_cnpj, 1=valor, 2=pr_patrimonio_liquido, 3=in_cnpj' -from fundo -to fundo
```
