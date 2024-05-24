# Maestro

Maestro é o nome dado a nosso **orquestrador de pipelines de captura e
atualização de dados**. 

O Maestro é construído com o software
[Dagster](https://dagster.io/) e é executado em ambiente [Docker](https://www.docker.com/).

## Instalação
### Requisitos

- Docker
- Python >= 3.8 (outras versões não são testadas)

### Pré-configuração

1. Clone o repositório oficial do Maestro:
```bash
git clone https://github.com/RJ-SMTR/maestro && cd maestro/
```
2. Crie um ambiente virtual e instale o maestro:
```bash
# cria ambiente
python -m venv .maestro
# ativa ambiente
. .maestro/bin/activate
# instala maestro
pip3 install -e .
```
3. Prepare um arquivo com variáveis de ambiente chamado `.env_local`. Ele deve seguir o seguinte modelo (preencher somente onde é requisitado):
```
MODE=dev
DAGSTER_POSTGRES_USER=postgres
DAGSTER_POSTGRES_PASSWORD=123456
DAGSTER_POSTGRES_HOST=localhost
DAGSTER_POSTGRES_DB=postgres
REDIS_HOST=localhost
BQ_PROJECT_NAME=rj-smtr-dev
SENSOR_BUCKET=rj-smtr-dev
BASEDOSDADOS_CONFIG=<preencher>
BASEDOSDADOS_CREDENTIALS_PROD=<preencher>
BASEDOSDADOS_CREDENTIALS_STAGING=<preencher>
DAGSTER_HOME=<preencher>
```

- Preencha `DAGSTER_HOME` com o caminho do repositório do maestro + `.dagster_workspace`. Exemplo: `/home/user/git_repos/maestro/.dagster_workspace`

- Preencha `BASEDOSDADOS_CONFIG` com o resultado de `cat $HOME/.basedosdados/config.toml | base64`, considerando que você possui as credenciais de **DEV**

- Preencha `BASEDOSDADOS_CREDENTIALS_PROD` e `BASEDOSDADOS_CREDENTIALS_STAGING` com o resultado de `cat .basedosdados/credentials/prod.json | base64`, considerando que você possui as credenciais de **DEV**

## Ligando os serviços

### Configurando o Docker

Caso esteja rodando pela primeira vez **ou tenha adicionado uma nova dependência**, execute a configuração do Dagster com:

```bash
# ativando o ambiente (se não estiver ativo)
. .maestro/bin/activate
# configurando o maestro
maestro setup
```

Este comando instala as dependências, configura o container (Docker) no
qual o Dagster ficará ativo e indica qual *host* deve ser usado (neste
caso, o *localhost*).

### Ativando o Maestro

Para ativar o Maestro, execute o comando:

```bash
# ativando o ambiente (se não estiver ativo)
. .maestro/bin/activate
# rodando o maestro
maestro up
```

Com isso, o Dagit ficará disponível em
[http://localhost:3000/](http://localhost:3000/). Os logs do daemon,
dagit e gRPC serão todos redirecionados ao mesmo terminal.

### Desativando
Para desativar o Maestro, basta fechar seu terminal (Ctrl+C).

Caso queira remover as imagens criadas no Docker (por espaço ou
necessidade de uso das respectivas portas), rode:

```bash
# removendo imagens Docker do maestro
maestro down
```

!!! Warning "Atenção!"
    Uma vez removidas as imagens, é necessário configurar novamente o
    Docker antes ativar o Dagster, rodando:
    ```bash
    maestro setup
    maestro up
    ```