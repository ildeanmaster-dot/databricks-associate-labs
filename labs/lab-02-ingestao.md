# Lab 2: Ingestão (COPY INTO e Auto Loader)

**Situação:** concluído em 19/11/2026.
**Seção do guia:** 2, Data Ingestion & Loading (21%).
**Caderno de Estudos:** A4 ingestão.

## Objetivo

Carregar os mesmos 6 CSVs de duas formas e comparar o comportamento diante de coluna nova e de valor inválido.

## Dados

Ver [docs/02-ambiente.md](../docs/02-ambiente.md#dados-do-lab). O arquivo 03 traz a coluna nova `canal_venda`. O arquivo 04 tem, na linha 1016, `data_pedido = NAO_INFORMADO` e `quantidade = ABC`.

## Parte A: COPY INTO (`lab_de.bronze.pedidos_copy`)

### Resultados reais

| Arquivo | Resultado |
|---|---|
| 01 e 02 | 10 linhas; tipos int, date, string e double |
| 03 | schema evoluiu com `mergeSchema`; 14 linhas, 4 com `canal_venda` |
| 04 | `VALIDATE ALL` falhou com `DELTA_FAILED_TO_MERGE_FIELDS` em `data_pedido` |
| Checagem | 3 de 3 |

### Roteiro de referência

```sql
-- Cria a tabela alvo vazia; o COPY INTO precisa de uma tabela Delta existente
CREATE TABLE IF NOT EXISTS lab_de.bronze.pedidos_copy;
-- Carrega os arquivos 01 e 02 da pasta raw
COPY INTO lab_de.bronze.pedidos_copy
-- Origem: o volume com os CSVs
FROM '/Volumes/lab_de/bronze/raw/'
-- Formato dos arquivos
FILEFORMAT = CSV
-- Glob que seleciona só os arquivos 01 e 02
PATTERN = 'pedidos_2026_0[12]*'
-- Lê o cabeçalho e infere os tipos das colunas
FORMAT_OPTIONS ('header' = 'true', 'inferSchema' = 'true')
-- Permite criar o schema na primeira carga e evoluir nas próximas
COPY_OPTIONS ('mergeSchema' = 'true');
```

```sql
-- Valida o arquivo 04 sem gravar nada na tabela (checa parse, schema e constraints)
COPY INTO lab_de.bronze.pedidos_copy
-- Mesma origem
FROM '/Volumes/lab_de/bronze/raw/'
-- Mesmo formato
FILEFORMAT = CSV
-- Só valida; o resultado foi DELTA_FAILED_TO_MERGE_FIELDS em data_pedido
VALIDATE ALL
-- Só o arquivo com valor inválido
PATTERN = 'pedidos_2026_04*'
-- Mesmas opções de leitura
FORMAT_OPTIONS ('header' = 'true', 'inferSchema' = 'true')
-- Mesma opção de evolução
COPY_OPTIONS ('mergeSchema' = 'true');
```

## Parte B: Auto Loader (`lab_de.bronze.pedidos_auto`)

### Resultados reais

| Etapa | Resultado |
|---|---|
| 01 e 02 | 10 linhas; `_rescued_data` criada automaticamente |
| Reexecução sem arquivo novo | não duplicou (checkpoint) |
| 03 | o stream parou uma vez com `UNKNOWN_FIELD_EXCEPTION.NEW_FIELDS_IN_FILE` (`[canal_venda]`, retry automático true); a reexecução carregou: 14 linhas, 4 com canal |
| 04 | carregou sem erro: 17 linhas; a linha 1016 ficou com `data_pedido` e `quantidade` vazios e `_rescued_data` com JSON de `NAO_INFORMADO`, `ABC` e o caminho do arquivo |
| 05 e 06 | 22 linhas, 1 com `_rescued_data`, 12 com `canal_venda` |
| Checagem | 3 de 3 |

### Roteiro de referência (notebook Python)

Antes: criar os volumes `lab_de.bronze.landing` (chegada) e `lab_de.bronze.ctrl` (controle) e copiar os arquivos para `landing` um de cada vez.

```python
# Caminho onde os arquivos chegam; o Auto Loader observa esta pasta
origem = "/Volumes/lab_de/bronze/landing/"
# Pasta de controle FORA da pasta observada (senão ele lê os próprios arquivos de controle)
controle = "/Volumes/lab_de/bronze/ctrl/pedidos_auto"
# Lê em modo streaming usando o Auto Loader (formato cloudFiles)
df = (spark.readStream.format("cloudFiles")
      # Os arquivos de origem são CSV
      .option("cloudFiles.format", "csv")
      # Usa a primeira linha como nome das colunas
      .option("header", "true")
      # Onde o Auto Loader guarda o schema inferido e suas versões
      .option("cloudFiles.schemaLocation", f"{controle}/schema")
      # Contrato de tipos: valor que não couber vai para _rescued_data
      .option("cloudFiles.schemaHints", "pedido_id INT, data_pedido DATE, quantidade INT, valor_unitario DOUBLE")
      # Começa a leitura da pasta de chegada
      .load(origem))
# Grava o stream na tabela Delta
(df.writeStream
   # Checkpoint: guarda quais arquivos já foram processados (evita duplicar)
   .option("checkpointLocation", f"{controle}/checkpoint")
   # Processa tudo o que está disponível e para (único trigger aceito no serverless além do Once)
   .trigger(availableNow=True)
   # Tabela de destino no Unity Catalog
   .toTable("lab_de.bronze.pedidos_auto"))
```

Sem `schemaEvolutionMode` informado e sem schema completo, o modo é `addNewColumns`: adiciona a coluna nova e para o stream uma vez. Por isso o arquivo 03 parou e a reexecução carregou.

## Contraste (o que cai na prova)

No arquivo 04, o `VALIDATE` do COPY INTO falhou. O Auto Loader carregou a linha e guardou os valores ruins em `_rescued_data`.

| | COPY INTO | Auto Loader |
|---|---|---|
| Tipo | comando SQL batch, idempotente | streaming incremental com checkpoint |
| Arquivo já carregado | pulado (mesmo se modificado); `force` desliga | pulado pelo checkpoint |
| Coluna nova | evolui com `mergeSchema` | `addNewColumns` adiciona e para; reexecutar carrega |
| Valor inválido | falha a carga | vai para `_rescued_data` (com `schemaHints`) |
| Muitos arquivos | a Databricks recomenda Auto Loader | recomendado |

Fontes: learn.microsoft.com/azure/databricks/ingestion/cloud-object-storage/auto-loader/schema e /sql/language-manual/delta-copy-into (conferidas em 19/11/2026).
