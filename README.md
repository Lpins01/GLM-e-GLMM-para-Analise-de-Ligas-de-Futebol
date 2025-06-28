# Comparando Ligas com GLM e GLMM

Este projeto explora as dinâmicas do futebol nas principais ligas do mundo através de uma perspectiva estatística. A questão central investigada é: os fatores que influenciam o resultado de uma partida, como a vantagem de jogar em casa (mando de campo), são universais ou variam significativamente de uma liga para outra?

Para responder a essa pergunta, utilizamos dados de partidas da temporada 2023/2024 e comparamos o desempenho de dois poderosos modelos estatísticos:

1.  **Modelo Linear Generalizado (GLM):** Fornece uma visão "agregada", tratando todas as ligas como uma única população e estimando um efeito médio geral.
2.  **Modelo Linear Generalizado Misto (GLMM):** Leva em conta a estrutura hierárquica dos dados (jogos aninhados dentro de ligas), permitindo que os efeitos variem e, assim, revelando as características únicas de cada competição.

O objetivo final não é apenas analisar os fatores do jogo, mas também demonstrar na prática como e por que os modelos de efeitos mistos (GLMM) são mais adequados e oferecem insights mais ricos para analisar dados com agrupamentos naturais, como é o caso de competições esportivas.

# Obtenção dos Dados - API-Football

Esta seção detalha o processo de coleta dos dados brutos utilizados no projeto.

## Fonte de Dados

Os dados foram extraídos da API-Football, uma API abrangente que fornece dados de partidas de futebol de diversas ligas ao redor do mundo. Para este projeto, foi utilizado o plano de acesso gratuito (Free Plan).

Uma das principais limitações do plano gratuito é o acesso restrito a dados de temporadas mais antigas. Por essa razão, a análise se concentra na temporada de 2023/24 das ligas

## Processo de Coleta

A coleta de dados foi automatizada através de um script em Python `requisicao_dados.ipynb`, seguindo os seguintes passos:

1.  **Autenticação Segura:** A chave de acesso da API foi guardada em um arquivo .env para garantir que as credenciais não fossem expostas no código-fonte.

2.  **Requisição à API:** Foi feita uma chamada ao endpoint /fixtures da API-Football para obter os dados de todas as partidas da temporada de 2023 de cada campeonato.

3.  **Estruturação e Consolidação:** A resposta da API (em formato JSON) foi processada, e os dados de cada partida foram extraídos e organizados em um único DataFrame do pandas.

## Tratamento dos Dados

Após a obtenção dos dados, foi realizada uma etapa de pré-processamento e limpeza para adequar o dataset às análises e à modelagem. O objetivo foi remover informações desnecessárias, simplificar a estrutura e criar variáveis relevantes para os modelos GLM e GLMM.

O processo foi dividido nas seguintes etapas:

1. **Remoção de Colunas Irrelevantes e Redundantes:**

Diversas colunas foram removidas para otimizar o DataFrame. Elas foram agrupadas de acordo com o motivo da exclusão:

- **Dados Redundantes:** Informações que podiam ser encontradas ou calculadas a partir de outras colunas.
    - `fixture.timestamp` (redundante com `fixture.date`)
    - `fixture.status.long` (redundante com `fixture.status.short`)
    - `teams.home.winner` e `teams.away.winner` (calculável a partir dos gols)
    - `score.fulltime.home` e `score.fulltime.away` (redundante com `goals.home` e `goals.away`)

- **Dados de Exibição (Não Analíticos):** Colunas que continham URLs de imagens ou logos, sem valor para a análise estatística.
    - `league.logo`
    - `league.flag`
    - `teams.home.logo`
    - `teams.away.logo`

- **Informações Irrelevantes para a Modelagem:** Detalhes que não contribuem para prever o resultado das partidas.
    - `fixture.referee`
    - `fixture.timezone`
    - `fixture.periods.first` e `fixture.periods.second`
    - `fixture.status.elapsed`
    - `league.standings`