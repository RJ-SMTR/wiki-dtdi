# Infraestrutura da SMTR

## Visão geral

A infraestrutura da SMTR é construída em cima do Google BigQuery. Os projetos estão divididos em:

- **dev**: ambiente de teste;
- **staging**: tabelas de staging do Base dos Dados no BigQuery
- **prod**: tabelas finais para consumo de outras áreas e aplicações.

Todo código das pipelinas de captura e/ou atualização das tabelas em desenvolvimento e produção estão
versionados no [Github](https://github.com/orgs/RJ-SMTR/).

![](../imgs/infra-overview.png)

## Datalake

### Visão geral

- Dagster/Maestro: Orquestrador de pipelines de captura e atualização de dados.
    - Gerenciamento de queries (maestro-bq)
    - Qualquer erro na pipeline é avisado no Discord

![](../imgs/infra-datalake.png)

### Pipelines
- Lista das pipelines implementadas
- Fluxo de dados (dag)