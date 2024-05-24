## O que é DevOps?

O termo *DevOps* é a contração e junção dos termos *development* (dev) e *operations* (ops). É um conjunto de práticas que visa reduzir o ciclo de entregas das equipes de desenvolvimento por meio de automações de  tarefas recorrentes (como compilação e deploy) e padronização de ambientes .

Na SMTR, a equipe de *DevOps* é responsável pela configuração dos contêineres onde rodam as aplicações, da infraestrutura de Cloud, pelos processos entrega e integração contínua (CI/CD) e observabilidade. 


## Infraestrutura de Cloud da SMTR

A infraestrutura de Cloud da SMTR está hospedada na Google Cloud Platform (GCP), e pode ser acessada por meio deste link:
[https://console.cloud.google.com/](https://console.cloud.google.com/)

### Clusters

O ambiente de produção **rj-smtr** possui três clusters:

- **rj-smtr-dev**: ambiente de desenvolvimento
- **rj-smtr-gke**: ambiente de produção onde são executadas as *pipelines* de dados
- **rj-smtr**: ambiente de produção onde são executadas as demais aplicações

#### rj-smtr-dev

O ambiente de desenvolvimento é completamente separado dos ambientes de produção. Desta forma, evita-se tanto conflito de dependências, quanto disputa por recursos.

#### rj-smtr-gke

Existem dois *pods* neste *cluster*:

- prefect
- redis

##### prefect
O *pod prefect* é *pod* que executa as *pipelines* de dados, por exemplo:

- captura de dados minuto a minuto;
- captura de dados hora em hora;
- materialização de tabela;
- consolidação de dados diária.

As *pipelines* são controladas por meio do [Prefect](https://www.prefect.io/), seu código pode ser encontrado [neste repositório](https://github.com/prefeitura-rio/pipelines) e sua documentação [neste link](https://docs.dados.rio/guia-desenvolvedores/visao-geral-infra/#introducao-ao-fluxo-de-dados).

##### redis
Paralelamente ao *Prefect* que executa as *pipelines*, é executado o banco de dados em memória *redis*. Se faz necessário usar tal tecnologia para cache. A quantidade de dados adquirida momento-a-momento pela *pipeline* é muito grande. Escrever os dados na *GCP* em tempo real eleva muito os custos. Então, faz-se o cache no *redis* e escreve-se os dados de minuto em minuto, hora em hora, e até em consolidações diárias.

#### rj-smtr
Neste *cluster* existem duas aplicações com seus respectivos *front-ends* e *back-ends*, e ambientes de *staging* e produção.

##### mobilidade-rio-api (staging e produção)
É o pod que contém a API responsável pelo *back-end* do *app* [https://mobilidade.rio/](https://mobilidade.rio/)

A API é escrita em **Django** e seu repositório pode ser acessado [aqui](https://github.com/RJ-SMTR/mobilidade-rio-api).

O ambiente de *staging* está contido no *namespace* **mobilidade-v2-staging** e pode ser acessado em [https://api.staging.mobilidade.rio/](https://api.staging.mobilidade.rio/)

O ambiente de produção está contido no *namespace* **mobilidade-v2** e pode ser acessado em [https://api.mobilidade.rio/](https://api.mobilidade.rio/)

##### mobilidade-rio-app (staging e produção)
É o pod que contém o *front-end* do *app* [https://mobilidade.rio/](https://mobilidade.rio/)

A interface de usuário é escrita em **React** e seu repositório pode ser acessado [aqui](https://github.com/RJ-SMTR/mobilidade-rio-app).

O ambiente de *staging* também está contido no *namespace* **mobilidade-v2-staging** e pode ser acessado em [https://app.staging.mobilidade.rio/](https://app.staging.mobilidade.rio/)

O ambiente de produção também está contido no *namespace* **mobilidade-v2** e pode ser acessado em [https://mobilidade.rio/](https://mobilidade.rio/)

##### data-tools-api e data-tools-ui (staging e produção)
Tanto o *back-end* quanto o *front-end* utilizam a ferramenta **IBI Transit Data Tools**, sua documentação pode ser encontrada [aqui](https://data-tools-docs.ibi-transit.com/en/latest/).

Os repositórios estão acessíveis em:

- [https://github.com/RJ-SMTR/sigmob-api](https://github.com/RJ-SMTR/sigmob-api) (API)
- [https://github.com/RJ-SMTR/sigmob-app](https://github.com/RJ-SMTR/sigmob-app) (interface de usuário)

O ambiente de *staging* utiliza o *namespace* **gtfs-editor-staging** e é acessível em [https://gtfs-editor.staging.mobilidade.rio/](https://gtfs-editor.staging.mobilidade.rio/)

O ambiente de produção utiliza o *namespace* **gtfs-editor** e é acessível em [https://gtfs-editor.mobilidade.rio/](https://gtfs-editor.mobilidade.rio/)

## CI/CD

### Versionamento
Todos os projetos são versionados com a ferramenta *Git*, hospedados no *Github* e podem ser encontrados nos seguintes perfis:

- [Secretaria de Transportes do Rio de Janeiro](https://github.com/RJ-SMTR)
- [Prefeitura do Rio de Janeiro](https://github.com/prefeitura-rio/)

### Deploy
Os processos de *deploy* são feitos por meio da ferramenta *Github Actions*. No *Github Actions*, o *deploy* é acionado por meio de um gatilho. Sempre que um *commit* é feito em uma *branch* que possua um processo de *deploy* configurado, um *script* de *workflow* é executado.

O *script* de *workflow* configura e executa diversas tarefas, como, por exemplo:

- definição de variáveis de ambiente;
- compilação de contêineres e upload das imagens para a nuvem;
- instalação de dependências e certificados;
- execução de testes unitários;
- execução de scripts necessários para inicialização;
- compilação e execução da aplicação;
- demais etapas de configuração que se fizerem necessárias.

Ao fim, se todas as etapas ocorreram sem nenhum problema, a nova versão entra em execução.

A documentação do *Github Actions* pode ser encontrada [neste link](https://docs.github.com/en/actions).

## Observabilidade

No momento, o projeto observabilidade está sendo desenvolvido. Por enquanto, há duas ferramentas em funcionamento para observabilidade.

- Monitor de *uptime* de diversos serviços que são executados na nuvem: [https://uptime.mobilidade.rio/status/monitor](https://uptime.mobilidade.rio/status/monitor)
- Alertas de *uptime*: a todo o momento as aplicações são testadas com *webhooks*, conforme seu comportamento muda, alertas são emitidos no canal do *Discord* da SMTR.