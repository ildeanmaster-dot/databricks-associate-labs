# Erros reais e lições

Erros vistos nos labs, com a mensagem exata, a causa e o conceito de prova que cada um treina.

## Vistos neste workspace (19/11/2026)

| Lab | Mensagem | Causa | Conceito de prova |
|---|---|---|---|
| 2 (COPY INTO) | `DELTA_FAILED_TO_MERGE_FIELDS` em `data_pedido` no `VALIDATE ALL` do arquivo 04 | `NAO_INFORMADO` não cabe na coluna DATE já existente na tabela | COPY INTO aplica o schema da tabela; não resgata valor inválido |
| 2 (Auto Loader) | `UNKNOWN_FIELD_EXCEPTION.NEW_FIELDS_IN_FILE` com `[canal_venda]`, retry automático true | modo padrão `addNewColumns`: adiciona a coluna e para o stream | Evolução de schema do Auto Loader; reexecutar carrega com o schema novo |

Contraste do Lab 2 no arquivo 04: o `VALIDATE` do COPY INTO falhou; o Auto Loader carregou a linha e guardou os valores ruins em `_rescued_data`.

## Armadilhas já testadas em outros workspaces (skills, set/2026)

| Situação | Mensagem ou sintoma | Correção |
|---|---|---|
| COPY INTO sem cast com `inferSchema` desligado | `DELTA_FAILED_TO_MERGE_FIELDS` (tudo vira STRING) | `COPY INTO t FROM (SELECT CAST(a AS INT) a, ... FROM '<volume>')` |
| Auto Loader sem `schemaHints` | `_rescued_data` vazio mesmo com dado sujo | Declarar `cloudFiles.schemaHints` (sem contrato não existe dado sujo) |
| Checkpoint dentro da pasta escaneada | Auto Loader lê os próprios arquivos de controle | Volume separado para checkpoint |
| `CREATE OR REPLACE TABLE` numa fonte de stream | `DIFFERENT_DELTA_TABLE_READ_BY_STREAMING_SOURCE` | `CREATE TABLE IF NOT EXISTS` + `INSERT INTO`; se quebrou, full refresh |
| Stream sem trigger no serverless | `INFINITE_STREAMING_TRIGGER_NOT_SUPPORTED` | `.trigger(availableNow=True)` |
| `CREATE OR REPLACE STREAMING TABLE` | erro de sintaxe | Streaming table usa `CREATE OR REFRESH` |
| `CREATE OR REFRESH STREAMING TABLE t;` sem query | `STREAMING_TABLE_OPERATION_NOT_ALLOWED.UNSUPPORTED_OPERATION` | `REFRESH STREAMING TABLE t [FULL];` |
| Código `@dp.table` rodado no notebook | "The DLT module is not supported on this cluster" | É esperado: código de pipeline só roda dentro do pipeline |
| Arquivo sem extensão na pasta do pipeline | "This file type is not supported in the pipeline source folder" | Usar `.py` ou `.sql` |
| `taskValues.get` fora do job | `TypeError: Must pass debugValue...` | Passar `debugValue` |
| CLI antiga no PATH | `bundle` não reconhecido | `pip uninstall databricks-cli` |
| `CLUSTER BY` em coluna BOOLEAN, ARRAY, MAP ou STRUCT | `DELTA_CLUSTERING_COLUMNS_DATATYPE_NOT_SUPPORTED` | Conferir tipos com `DESCRIBE` antes |

## Erros de prova registrados nas checagens

- Lab 1, pergunta 3: errada no primeiro teste, corrigida. O enunciado não ficou registrado neste repositório.
