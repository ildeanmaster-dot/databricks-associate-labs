# Ambiente

## Workspace (visto em print, 19/11/2026)

- Endereço: `dbc-c699beda-0bbc.cloud.databricks.com`, nome "workspace".
- O topo mostra o botão **Manage Trial**. Isso indica conta de teste (trial), não Free Edition: a documentação da Free Edition não descreve trial e diz que ela não tem console de conta. **Pendência:** abrir Manage Trial e registrar a data de término e os limites.
- Menu lateral visto: New, Workspace, Recents, Catalog, Jobs & Pipelines, Compute, Discover, Marketplace; Analytics: Dashboards, Genie Agents, Alerts, Visual Data Prep; Lakehouse: SQL Editor, Queries, Query History, SQL Warehouses; Data Engineering: Runs, Data Ingestion; AI/ML: Playground, Agents, AI Gateway, Experiments.

## Convenção do lab

```
lab_de                      catálogo do projeto (default storage)
├── bronze                  dado cru
│   ├── raw        (volume) os 6 CSVs originais
│   ├── landing    (volume) chegada do Auto Loader
│   ├── ctrl       (volume) schema location e checkpoint
│   ├── pedidos_copy        tabela do COPY INTO
│   └── pedidos_auto        tabela do Auto Loader
├── silver                  dado limpo (Lab 3)
└── gold                    dado modelado (Lab 3)
```

Recomendação oficial: camadas como schemas dentro de um catálogo, tabelas managed, volumes do Unity Catalog como landing zone, checkpoint fora da pasta escaneada.

## Dados do lab

Os 6 CSVs estão em `/Volumes/lab_de/bronze/raw/`. Origem local: `D:\Portifolio\Databricks\databricks-de-lab\data`, copiados para `D:\Treinamento\databricks\data` por script PowerShell.

| Arquivo | O que tem de especial |
|---|---|
| `pedidos_2026_01` | dados limpos |
| `pedidos_2026_02` | dados limpos |
| `pedidos_2026_03_nova_coluna` | traz a coluna nova `canal_venda` |
| `pedidos_2026_04_tipo_invalido` | linha 1016 com `data_pedido = NAO_INFORMADO` e `quantidade = ABC` |
| `pedidos_2026_05` | dados com `canal_venda` |
| `pedidos_2026_06` | dados com `canal_venda` |

Total: 22 linhas de dados.

Colunas confirmadas pelos `schemaHints` do Lab 2: `pedido_id INT`, `data_pedido DATE`, `quantidade INT`, `valor_unitario DOUBLE`, mais `canal_venda` (string) e `_rescued_data`. As demais colunas dos CSVs não ficaram registradas; a parte 1 do Lab 3 mostra a lista completa.

## Limites da Free Edition (doc oficial, conferida em 19/11/2026)

Se a conta for trial, estes limites podem não valer. Confirmar.

- Só serverless (sem cluster customizado, sem escolha de runtime).
- 1 SQL warehouse, tamanho máximo 2X-Small.
- No máximo 5 tarefas de job concorrentes.
- 1 pipeline ativo por tipo.
- Sem R e Scala; sem RDD API.
- Sem local de armazenamento customizado.
- Internet de saída restrita (a verificação pelo LinkedIn libera).
- 1 workspace e 1 metastore; sem console de conta.
- Sem Spark UI no serverless: diagnóstico pelo query profile.
- Cache (`cache()`, `persist()`, `CACHE TABLE`) lança exceção no serverless.
- Streaming só com `trigger(availableNow=True)`.
- Serverless aceita só 6 propriedades Spark: `spark.databricks.execution.timeout`, `spark.sql.legacy.timeParserPolicy`, `spark.sql.session.timeZone`, `spark.sql.shuffle.partitions`, `spark.sql.ansi.enabled`, `spark.sql.files.maxPartitionBytes`.

Fontes: learn.microsoft.com/azure/databricks/getting-started/free-edition-limitations, /compute/serverless/limitations, /spark/conf.

## Pendências de ambiente

| Para o lab | O que fazer |
|---|---|
| Todos | Confirmar trial em Manage Trial (data de término e limites) |
| Lab 6 | `winget install Databricks.DatabricksCLI`; `databricks auth login --host https://dbc-c699beda-0bbc.cloud.databricks.com --profile DEFAULT` no **Windows Terminal**; remover `databricks-cli` antigo do PATH (`pip uninstall databricks-cli`) |
| Lab 8 | Criar 2 grupos em Settings, Identity and access |
| Lab 15 (Fase 2) | Verificação LinkedIn, se for Free Edition, para liberar internet de saída |
| Labs 17 e 20 (Fase 2) | Teste de 5 min: Git folder com GitHub; external location |

## Armadilhas de ambiente já vistas (Windows)

- A janela clássica do Windows PowerShell corrompe texto colado longo. Use o Windows Terminal ou coloque o comando num `.ps1`.
- `databricks auth login` sem `--host` abre um seletor que o console antigo não desenha. Sempre passe `--host`.
- Duas CLIs no PATH (a antiga pelo pip não entende `bundle`).
