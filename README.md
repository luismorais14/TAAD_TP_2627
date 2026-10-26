# [Nome do Projeto]: Data Warehouse para análise de incêndios

Trabalho prático da unidade curricular **Tópicos Avançados de Armazenamento de Dados**
(Mestrado em Engenharia Informática, ESTG – Politécnico do Porto, 2026/2027).

> Sistema de Business Intelligence com Data Warehouse, arquitetura *medallion*
> (Bronze, Silver, Gold), camada de proveniência W3C PROV-O e dashboards de análise.

## Autores

| Nome | Número |
|---|---|
| Luís [Apelido] | [nº] |
| [Nome] | [nº] |
| [Nome] | [nº] |

## Caso de estudo

[Descrever em 3 a 5 linhas o caso de estudo, os requisitos de análise e a relevância do sistema.]

**Questões de negócio a que o sistema responde:**

- [ex.: Como evolui o número de ocorrências por distrito e por mês?]
- [ex.: Que recursos são mobilizados por tipologia de incêndio?]
- [ex.: Qual a relação entre as condições meteorológicas e a intensidade das ocorrências?]

## Fontes de dados

| Fonte | Grão (um registo =) | Frequência | Deteção de novos registos | Natureza |
|---|---|---|---|---|
| [ex.: Fogos.pt / ANEPC] | uma ocorrência | [contínua] | [id incremental / timestamp] | Operacional |
| [ex.: IPMA] | uma leitura de estação | [horária] | [timestamp] | Operacional |
| [ex.: ICNF / INE] | [grão] | [anual] | [n/a] | Complementar |

## Arquitetura

```
Fontes ──► Bronze ──► Silver ──► Gold (Data Warehouse)
             │           │            │
             └───────────┴────────────┴──► Proveniência (PROV-O)

Orquestração (único ponto) + Tabelas de controlo (execuções e marcas de água)
```

- **Bronze:** dados brutos, tal como extraídos de cada fonte (um extrator por fonte).
- **Silver:** dados limpos, validados e padronizados (uma transformação por entidade).
- **Gold:** modelo dimensional (um carregador por dimensão ou tabela de factos).
- **Proveniência:** registo em PROV-O de entidades (fontes e tabelas), atividades (execuções) e agentes.

### Modelo dimensional

- **Tabelas de factos:** `[FactX]` (grão: …), `[FactY]` (grão: …)
- **Dimensões:** `[DimLocal]`, `[DimTipologia]`, `[DimCausa]`, `[DimMeio]`, `[DimTempo]`, …
- **Dimensão com história (SCD2):** `[DimX]`

### Princípios do processo de povoamento (ETL)

- Componentes coesos, com uma única responsabilidade e uma única transição entre camadas.
- Baixo acoplamento: comunicação apenas através das tabelas e ficheiros das camadas (contratos documentados).
- Orquestração separada, num único ponto e sem lógica de transformação.
- Configuração separada do código (ficheiros de configuração e variáveis de ambiente).
- Povoamento inicial e incremental com os mesmos componentes, diferindo apenas nos parâmetros e na marca de água por fonte.
- Componentes idempotentes: repetir uma carga não gera duplicados.
- Registo de cada execução (início, fim, estado, registos lidos, inseridos e rejeitados).

## Tecnologias

- Armazenamento / Data Warehouse: [ex.: SQL Server / PostgreSQL]
- ETL: [ex.: SSIS / Airflow / Python]
- Orquestração: [ex.: pacote mestre / DAG / Makefile]
- Proveniência: [ex.: triplestore / ficheiros RDF (Turtle)]
- Visualização: [ex.: Power BI / Metabase / Superset]
- Outras (opcional): [ex.: Data Lake, base de dados de grafos]

## Estrutura do repositório

```
.
├── fontes/            # Fontes de dados utilizadas e documentação de extração
├── povoamento/
│   ├── bronze/        # Extratores (fonte → Bronze)
│   ├── silver/        # Transformações (Bronze → Silver)
│   ├── gold/          # Carregadores (Silver → Gold)
│   ├── orquestracao/  # Ponto único de orquestração
│   ├── config/        # Configuração (sem credenciais reais)
│   └── controlo/      # Tabelas de controlo e contratos entre camadas
├── proveniencia/      # Modelo PROV-O e consultas
├── bpmn/              # Modelos conceptuais (povoamento inicial e incremental)
├── backup/            # Backup e scripts de criação do sistema
├── dashboards/        # Ficheiros para reproduzir dashboards e relatórios
├── relatorio/         # Artigo (template LNCS)
├── demo/              # Passos para reproduzir a demonstração
├── LICENSE
└── README.md
```

## Como executar

### Pré-requisitos

- [Listar software e versões]
- [Listar contas ou chaves de API necessárias]

### Configuração

1. Copiar `povoamento/config/config.example.[ext]` para `povoamento/config/config.[ext]`.
2. Preencher ligações, caminhos e credenciais (nunca submeter credenciais para o repositório).

### Povoamento inicial

```bash
# [comando de orquestração com o parâmetro de carga inicial]
```

### Povoamento incremental

```bash
# [comando de orquestração com o parâmetro de carga incremental]
```

### Consulta da proveniência

```bash
# [comandos ou consultas para as três questões de proveniência]
```

### Visualização

[Indicar como abrir ou reproduzir os dashboards.]

O guia completo da demonstração (base de dados vazia, povoamento inicial, incremental,
proveniência e visualização) está em [`demo/`](demo/).

## Relatório

O artigo, em formato LNCS, está em [`relatorio/`](relatorio/).

## Licença

Distribuído sob a licença MIT. Consultar o ficheiro [LICENSE](LICENSE).

Os dados utilizados pertencem às respetivas entidades e estão sujeitos aos seus próprios
termos de utilização. Consultar a secção de fontes.
