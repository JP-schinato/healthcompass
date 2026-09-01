# Health Compass

Painel analítico de indicadores públicos do SUS (SIH, SIA e CNES), construído inteiramente na Oracle Cloud Infrastructure — projeto do **FIAP × Oracle Challenge 2026**.

> Orientando decisões. Salvando vidas.

## Sobre o projeto

| | |
|---|---|
| **Turma** | 1TSCO-B |
| **Grupo** | News Solution |
| **Sprint** | Sprint 2 — Desenvolvimento e Entrega |
| **Empresa parceira** | Oracle |

**Integrantes**

- Gabriel de Almeida Cruz — RM 572980 — Ingestão de dados e pipeline ETL
- João Pedro Silva Schinato — RM 569702 — Oracle Autonomous Database
- Matheus Raul Silvestre dos Santos — RM 572291 — Análise exploratória e camada visual
- Thalles William Ribeiro de Souza — RM 571201 — Oracle APEX e camada de consumo
- Paulo Moraes de Lima — RM 573329 — Oracle APEX e camada de consumo


## O problema

Secretarias de saúde e redes hospitalares têm dificuldade para monitorar em tempo real a capacidade assistencial e os perfis de atendimento do SUS. As respostas dependem de analistas SQL para cada consulta, o que atrasa decisões críticas de gestão.

## A solução

O Health Compass integra três bases públicas do Ministério da Saúde (SIH, SIA e CNES) em um pipeline analítico na Oracle Cloud, consolidando os dados em um modelo em camadas (Bronze/Silver/Gold) e disponibilizando os indicadores em um painel Oracle APEX.

## Arquitetura

```
BigQuery (basedosdados: br_ms_sih, br_ms_sia, br_ms_cnes)
        │  extração e carga em lote
        ▼
OCI Object Storage (bucket healthcompass-staging)
        │
        ▼
OCI Data Integration (tasks SQL/Pipeline) + OCI Data Flow (PySpark 3.5)
        │  Bronze → Silver → Gold
        ▼
Oracle Autonomous Database 23ai — schema HEALTHCOMPASS
        │
        ▼
Painel Oracle APEX (Dashboard · Comparativo por UF · Indicadores por UF · Catálogo)
```

Tudo dentro da Oracle Cloud Infrastructure — sem servidor próprio para manter.

### Camadas de dados

| Camada | Responsabilidade | Tabelas |
|---|---|---|
| **Bronze** | Dado cru, como veio da fonte | `BRONZE_SIH_SERVICOS_PROFISSIONAIS`, `BRONZE_SIH_SERV_PROF_BALANCED`, `BRONZE_SIA_PRODUCAO_AMBULATORIAL`, `BRONZE_CNES_ESTABELECIMENTO` |
| **Silver** | Dado limpo, tipado, sem duplicata | `SILVER_SIH_SERVICOS_PROFISSIONAIS`, `SILVER_SIA_PRODUCAO_AMBULATORIAL`, `SILVER_CNES_ESTABELECIMENTO` |
| **Gold** | Dado agregado, pronto para consumo (grão `ANO × MÊS × SIGLA_UF`) | `GOLD_SIH_RESUMO_UF`, `GOLD_SIA_RESUMO_UF`, `GOLD_CNES_RESUMO_UF` |

## Tecnologias utilizadas

- **Oracle Autonomous Database 23ai** — armazenamento relacional e camada Gold
- **OCI Data Integration** — orquestração das tasks SQL/Pipeline de ingestão e transformação
- **OCI Data Flow (Apache Spark 3.5 / Python)** — ETL do arquivo de Serviços Profissionais (SIH)
- **Oracle APEX** — painel interativo (Dashboard, Comparativo por UF, Indicadores por UF, Catálogo)
- **BigQuery / basedosdados.org** — fonte pública dos dados do SUS
- **Oracle Cloud Infrastructure (OCI)** — infraestrutura de hospedagem e integração

## Estrutura sugerida do repositório

> Ajuste esta seção para refletir a organização real das pastas do repositório.

```
├── etl/                # scripts de ingestão e transformação (OCI Data Flow / PySpark)
├── sql/                 # scripts SQL das tasks de Data Integration e das camadas Bronze/Silver/Gold
├── docs/                 # documentação de gestão de projeto (Sprint 1 e Sprint 2)
├── evidencias/           # prints de evidência da entrega (console OCI, painel, etc.)
└── README.md
```

## Limitações declaradas

- **Carga em amostra** — a carga atual traz um recorte balanceado por estado, não o volume integral das bases; o painel apresenta distribuições e proporções, não totais absolutos do país.
- **Registro não é internação** — a fonte hospitalar usada é o arquivo de Serviços Profissionais: cada linha é um ato profissional, não uma internação.
- **Grão por estado** — a camada Gold consolida por UF; não há detalhamento municipal nesta entrega.

Nenhuma dessas limitações vem de falha de arquitetura — as camadas já estão preparadas para a carga completa.

## Próximos passos

- Substituir a amostra pela carga integral das competências
- Descer o grão de estado para município
- Retomar o Select AI (linguagem natural → SQL) como camada de consulta assistida
- Abertura como dado público com controle social

## Vídeo pitch

[link a inserir após a gravação]

## Licença / uso

Projeto acadêmico desenvolvido para o FIAP × Oracle Challenge 2026, com dados públicos do Ministério da Saúde (SUS).
