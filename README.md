# Supplier Quality Analysis: Data Warehouse para análise da qualidade de fornecedores

Trabalho prático da unidade curricular **Tópicos Avançados de Armazenamento de Dados**
(Mestrado em Engenharia Informática, ESTG – Politécnico do Porto, 2026/2027).

> Sistema de Business Intelligence com Data Warehouse, arquitetura _medallion_
> (Bronze, Silver, Gold), camada de proveniência W3C PROV-O e dashboards de análise
> da qualidade de fornecedores.

## Autores

| Nome             | Número  |
| ---------------- | ------- |
| Luís Morais      | 8230258 |
| Francisco Morais | 8230204 |

## Caso de estudo

[Descrever em 3 a 5 linhas o caso de estudo: a análise da qualidade de fornecedores, os
defeitos registados por fábrica, material e tipo de defeito, e o impacto em tempo de paragem.]

**Questões de negócio a que o sistema responde:**

- Que fornecedores têm mais defeitos e mais minutos de paragem associados?
- Como evolui a quantidade de defeitos por mês, ano e fábrica?
- Que tipos de defeito e que categorias de material concentram mais problemas?
- Como se compara o período atual com o mesmo período do ano anterior?
- [Acrescentar outras questões do grupo]

## Fontes de dados

| Fonte                                | Grão (um registo =)           | Frequência            | Deteção de novos registos | Natureza                |
| ------------------------------------ | ----------------------------- | --------------------- | ------------------------- | ----------------------- |
| Supplier Quality Analysis (Obvience) | [a confirmar após exportação] | [simulada por replay] | [data do registo]         | [Operacional / amostra] |
| [Fonte complementar, se existir]     | [grão]                        | [frequência]          | [mecanismo]               | Complementar            |

**Origem dos dados e atribuição:** os dados provêm de um ficheiro de exemplo fornecido pela
Obvience (www.obvience.com) para demonstração de Power BI, e estão contidos num modelo
Power Pivot. Qualquer utilização dos dados deve incluir a atribuição à Obvience e o aviso
de direitos de autor **obviEnce ©**.

**Simulação de carga contínua:** [descrever o mecanismo, por exemplo o replay ordenado por
data dos registos históricos, com a marca de água por fonte.]

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

- **Tabelas de factos:** `[FactDefeito]` (grão: …) e `[FactParagem]` ou outra, a validar com o docente
- **Dimensões:** `DimData`, `DimFornecedor`, `DimFabrica`, `DimDefeito`, `DimTipoDefeito`, `DimTipoMaterial`, `DimSubCategoria`
- **Dimensão com história (SCD2):** `[DimFornecedor ou DimFabrica]`
- **Medidas:** quantidade de defeitos, minutos de paragem, número de relatórios de defeito, comparação com o período homólogo

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
- [Listar contas ou chaves necessárias]

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

## Licença e atribuição

O código é distribuído sob a licença MIT. Consultar o ficheiro [LICENSE](LICENSE).

Os dados do caso de estudo pertencem à Obvience e são partilhados apenas para demonstração
de funcionalidades de Power BI. Qualquer utilização deve incluir a atribuição à Obvience
e o aviso **obviEnce ©**. A licença MIT não se aplica aos dados.
