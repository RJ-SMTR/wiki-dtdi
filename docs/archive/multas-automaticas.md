# Multas Automáticas - Metodologia

**Versão:** v1.0

**Data de início:** 19/10/21

## Visão geral

O Código Disciplinar do Serviço Público de Transporte de Passageiros por
meio de Ônibus do Município do Rio de Janeiro (Código de Conduta do SPPO), instituída via [decreto nº
36343](https://doweb.rio.rj.gov.br/apifront/portal/edicoes/imprimir_materia/31457/1900)),
determina uma série de condições para a operação, gestão e qualidade das
linhas municipais. Essas condições, quando não cumprias, resultam em
possíveis multas e outras sanções.

A partir de 2021, a SMTR passa a realizar o cálculo de multas
automáticas a partir da ferramenta desenvolvida de monitoramento por GPS, conforme [resolução nº 3413](https://doweb.rio.rj.gov.br/apifront/portal/edicoes/imprimir_materia/738149/4986).

As multas aplicadas através da ferramenta podem ser aplicadas em 3 casos:

- [Frota operante abaixo da determinada](#frota-operante-abaixo-da-frota-determinada)
- [Falha no GPS (**não implementado**)](#falha-no-gps)
- [Não operação da linha (**não implementado**)](#nao-operacao-da-linha)

Para a versão 1.0 da ferramenta, consideramos somente infrações de
**frota operante abaixo da determinada em horários de pico** como
passíveis de multa, com exceção de sábados e domingos.

## Definicões

- **Consórcio**: Empresa responsável pela operação dos itinerários das linhas. Usualmente, consórcios operam linhas com exclusividade.
- **Linha**: Conjunto de serviços, usualmente descrito por números, ex: 123, 720.
- **Serviço**: Conjunto de itinerários, usualmente descrito pela linha e
  identificador do serviço, ex: 123 RA, 720 PR. As linhas podem ter
  serviços normais e específicos (como 'Parador', 'Expresso', 'Noturno',
  etc).
- **Itinerário**: Trajetos executados por veículos que param em pontos específicos e tem frequência de saída ou frota determinada.
- **Frota determinada**: Número de veículos que deverão operar em uma linha, definido pela SMTR.
- **Frota operante**: Número de veículos distintos em operacão que
  emitem um sinal de gps em determinada faixa horária. Os veículos
  considerados em operacão são aqueles que (Art. 2 I da Resolução):
    - não estão em garagens (veja a [lista de garagens consideradas](https://docs.google.com/spreadsheets/d/1KwCnejLQRk2S2x1QmcInzHV7XXh4pKCalWKAMM74ngc/edit?usp=sharing));
    - *[NÃO IMPLEMENTADO] tem o serviço do gps associado ao mesmo trajeto que está sendo realizado no instante da emissão do sinal (ver documentação da pipeline de gps);*
    - *[NÃO IMPLEMENTADO] não tiveram velocidade menor que 3km/h por mais que 10 minutos consecutivos.*
- **Faixa horária**: intervalo de 10 minutos comecando no primeiro
  minuto da hora, ex: 6:00, 6:10, 6:20. A faixa horária será
  desconsiderada caso ocorra mais que 2 falhas na captura dos dados de
  GPS pela SMTR. A captura é executada a cada 1 minuto, portanto,
  espera-se 8 capturas a cada faixa horária.
- **Gravidade da multa (Art. 44)**: As multas para atos oriundos
  diretamente do permissionário/concessionário classificam-se em quatro
  categorias, de acordo com sua gravidade (Tabela 1)

## Tipos de multa

### Frota operante abaixo da frota determinada

O Código de Conduta do SPPO dispõe no Artigo 17.I sobre as condições para
aplicacão de multas aos consóricios que operarem linhas abaixo da frota
determinada.

#### Definição

Segundo o Código de Conduta do SPPO no Artigo 17.I, as condicões para
aplicacão da multa por frota operante abaixo da frota determinada são as seguintes:

- Se frota determinada <= 5, então o consórcio estará sujeito a multa sempre que a frota operante for menor que a frota determina.
- Se frota determinada > 5, então o consórcio estará sujeito a multa sempre que a frota operante for:
    - < 80% da frota determinada, para dias úteis
    - *[NÃO CONSIDERADO] < 50% da frota determinada, para sábados*
    - *[NÃO CONSIDERADO] < 40% da frota determinada, para domingos e feriados*
    
#### Regras específicas para aplicação

O Código de Conduta do SPPO não explicita como operacionalizar a
aplicacão das multas via gps. Portanto, a SMTR define junto à Resolução, um conjunto de regras quais são:

- Será aplicada **somente uma multa por pico da manhã e da tarde** (Art.
  2 II). Ou
   seja, cada linha está sujeita a no máximo 2 multas por dia. Os
   horários que determinam o pico são diferentes para cada consórcio
   dado a natureza de suas operações (ver Tabela 1)
- A frota operante será calculada para cada linha e faixa horária.
  Se as condições satisfizerem a definição acima, então a
  linha naquela faixa horária será considerada abaixo da frota
  determinada.

- As multas serão aplicadas para um pico se:
    - existirem 3 faixas horárias (30 minutos) consecutivas abaixo da frota determinada
    - existirem 8 faixas horárias (80 minutos) não consecutivas abaixo da frota determinada

### Falha no GPS (não implementado)

Segundo o Art. 17 X do Código de Conduta do SPPO, as condicões para
aplicacão da multa por falha de GPS são as seguintes:

```
IX – Suspender por 24 (vinte e quatro) horas ou mais, sem autorização prévia do Órgão Gestor de transportes do Município do Rio de Janeiro, a operação do sistema de
controle da linha através de GPS:
Infração – gravíssima
Penalidade – multa (Grupo E-1)

X – Suspender por 1 (uma) hora ou mais, sem autorização prévia do Órgão Gestor de transportes do Município do Rio de Janeiro, a operação do sistema de controle da linha
através de GPS:
Infração – grave
Penalidade – multa (Grupo E-2)
```

### Não operação da linha (não implementado)

Segundo o Art. 17 X do Código de Conduta do SPPO, as condicões para
aplicacão da multa por falha de GPS são as seguintes:

```
VII – Suspender por 24 (vinte e quatro) horas ou mais, sem autorização prévia do Órgão Gestor de Transportes do Município do Rio de Janeiro, a operação de uma linha ou
serviço, em um ou ambos os sentidos:
Infração – gravíssima
Penalidade – multa (Grupo E-1)

VIII – Suspender por 4 (quatro) horas ou mais, sem autorização prévia do Órgão Gestor de Transportes do Município do Rio de Janeiro, a operação de uma linha ou serviço,
em um ou ambos os sentidos:
Infração – gravíssima
Penalidade – multa (Grupo E-1)
```

## Estrutura dos dados

<!-- Para verificação das multas são geradas 3 tabelas principais, que estão
disponíveis no datalake da SMTR dentro de [`projeto_multa_automatica` -
TODO: add link em prod](): -->

Para verificação das multas são geradas 3 tabelas principais:

- Sumário de multas por linha (`sumario_multa_linha_onibus`)
- Detalhes de multa por linha (`detalhes_multa_linha_onibus`)
- Detalhes de multa por veículos (`detalhes_multa_veiculo_onibus`)

A documentação completa da pipeline de captura, tratamento e atualização
dos dados pode ser lida [aqui](../infra/multas-automaticas.md).

## Tabelas e figuras de referência

Tabela 1: Gravidade da multa

| Grupo | Gravidade | Valor |
| ----- | --------- | ----- |
| Grupo E-1 | Gravíssima | 520 (quinhentas e vinte) UFIR-RJ |
| Grupo E-2 | Grave | 260 (duzentas e sessenta) UFIR-RJ |
| Grupo E-3 | Média | 130 (cento e trinta) UFIR-RJ |
| Grupo E-4 | Leve | 65 (sessenta e cinco) UFIR-RJ |

Tabela 2: Picos por consórcio (ajustado horário de pico da manhã)

| Consórcio    | Pico Manhã  | Pico Tarde    |
| ------------ | ----------- | ------------- |
| Intersul     | 6:30 - 9:30 | 16:00 - 19:00 |
| Internorte   | 6:00 - 9:00 | 16:00 - 19:00 |
| Transcarioca | 6:00 - 9:00 | 16:00 - 19:00 |
| Santa Cruz   | 5:30 - 8:30 | 17:00 - 20:00 |

<!-- ### Sumário de multas por linha

| Coluna | Descrição | 
| id_multa	| 
| linha	| 
| vista	| 
| consorcio	| 
| data	| 
| tipo_dia	| 
| pico	| 
| faixa_horaria	| 
| frota_servico	| 
| frota_minima	| 
| frota_aferida	| 
| porcentagem_frota	| 
| tipo_multa	| 
| artigo_multa	| 
| prioridade	|  -->
