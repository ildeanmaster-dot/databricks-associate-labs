# Fatos oficiais conferidos

Cada fato tem fonte e data de conferência. Se um simulado contradisser algo daqui, vale a documentação oficial: confira a página antes de decidir.

## Delta Lake

| Fato | Fonte | Conferido |
|---|---|---|
| VACUUM: limite padrão de 7 dias (propriedade `delta.deletedFileRetentionDuration`); arquivos de log ficam 30 dias por padrão e não são removidos pelo VACUUM | learn.microsoft.com/azure/databricks/sql/language-manual/delta-vacuum e /tables/operations/vacuum | 19/11/2026 |
| PRIMARY KEY, FOREIGN KEY e UNIQUE são informativas (não impostas); só NOT NULL e CHECK são impostos e falham a transação quando violados | learn.microsoft.com/azure/databricks/tables/constraints | 19/11/2026 |
| Liquid clustering substitui partitioning e ZORDER; até 4 chaves; incompatível com os dois na mesma tabela | learn.microsoft.com/azure/databricks/tables/clustering | out/2026 |
| `CLUSTER BY AUTO` exige tabela managed do UC, DBR 15.4 LTS+ e predictive optimization | docs.databricks.com/aws/en/tables/clustering | set/2026 |
| Predictive optimization roda OPTIMIZE, VACUUM e ANALYZE; só tabelas managed do UC; não faz ZORDER | docs.databricks.com/aws/en/optimizations/predictive-optimization | set/2026 |
| Deletion vectors aceleram DELETE, UPDATE e MERGE sem reescrever arquivos | docs.databricks.com/aws/en/delta/deletion-vectors | set/2026 |

Visto no ambiente (Lab 1, 19/11/2026): a tabela nasce com `clusterByAuto: true`; OPTIMIZE automático (`auto: true`) apareceu no `DESCRIBE HISTORY`; o DELETE teve `numAddedFiles: 0` (deletion vectors).

## Ingestão

| Fato | Fonte | Conferido |
|---|---|---|
| `cloudFiles.schemaEvolutionMode`: `addNewColumns` (padrão sem schema informado), `none` (padrão com schema informado), `rescue`, `failOnNewColumns`, `addNewColumnsWithTypeWidening` | learn.microsoft.com/azure/databricks/ingestion/cloud-object-storage/auto-loader/schema | 15/11/2026 (corrigido: antes constava `rescue` como padrão com schema) |
| `addNewColumns` adiciona a coluna e **para** o stream com `UnknownFieldException`; a reexecução pega o schema novo | mesma página | 19/11/2026, visto no Lab 2 |
| `inferColumnTypes`: `false` no `cloudFiles` (Python) e `true` no `read_files` (SQL) | docs.databricks.com/aws/en/sql/language-manual/functions/read_files | set/2026 |
| `_rescued_data` guarda em JSON os valores que não couberam no schema, mais o caminho do arquivo | auto-loader/schema | 19/11/2026, visto no Lab 2 |
| COPY INTO é idempotente: pula arquivo já carregado, mesmo que tenha sido modificado depois; `force = true` desliga a idempotência | learn.microsoft.com/azure/databricks/sql/language-manual/delta-copy-into | 19/11/2026 |
| COPY INTO: `COPY_OPTIONS ('mergeSchema' = 'true')` evolui o schema; `VALIDATE [ALL \| n ROWS]` valida sem gravar (parse, schema, NOT NULL e CHECK); `FILES` (até 1000) e `PATTERN` não podem ser usados juntos | mesma página | 19/11/2026 |
| Para diretórios com muitos arquivos, a Databricks recomenda Auto Loader em vez de COPY INTO | mesma página | 19/11/2026 |
| Serverless só aceita `trigger(availableNow=True)` (ou `Once`, deprecado) | learn.microsoft.com/azure/databricks/compute/serverless/limitations | set/2026 |

## Transformação

| Fato | Fonte | Conferido |
|---|---|---|
| `count_if(expr)` conta as linhas em que a expressão booleana é verdadeira; retorna BIGINT | learn.microsoft.com/azure/databricks/sql/language-manual/functions/count_if | 19/11/2026 |
| `approx_count_distinct(expr[, relativeSD])` usa HyperLogLog++; erro padrão de até 5%, ajustável por `relativeSD`; retorna BIGINT | learn.microsoft.com/azure/databricks/sql/language-manual/functions/approx_count_distinct | 19/11/2026 |
| `UNION` sem modificador é `DISTINCT` (remove duplicadas); `UNION ALL` preserva duplicadas | learn.microsoft.com/azure/databricks/sql/language-manual/sql-ref-syntax-qry-select-setops | 19/11/2026 |
| Os dois lados de um set operator precisam ter o mesmo número de colunas (senão `NUM_COLUMNS_MISMATCH`) e tipos compatíveis (senão `INCOMPATIBLE_COLUMN_TYPE`) | mesma página | 19/11/2026 |
| `INTERSECT` tem precedência maior que `UNION` e `EXCEPT`; `MINUS` é sinônimo de `EXCEPT` | mesma página | 19/11/2026 |
| `DESCRIBE TABLE` mostra nome, tipo e comentário das colunas; `EXTENDED` mostra owner e mais metadados | learn.microsoft.com/azure/databricks/sql/language-manual/sql-ref-syntax-aux-describe-table | 19/11/2026 |
| Materialized view tem refresh incremental só em serverless; em compute clássico é recomputada | learn.microsoft.com/azure/databricks/ldp/incremental-refresh | out/2026 |

## Spark e performance

| Fato | Fonte | Conferido |
|---|---|---|
| `spark.sql.shuffle.partitions` padrão 200 (aceita `auto`) | docs.databricks.com/aws/en/optimizations/aqe | set/2026 |
| Broadcast em runtime (AQE): `spark.databricks.adaptive.autoBroadcastJoinThreshold` 30 MB; no planejamento do Spark, `spark.sql.autoBroadcastJoinThreshold` 10 MB | mesma página | set/2026 |
| Skew: Max duration mais de 50% acima do percentil 75 indica skew; se o AQE não resolver, salting ou pré-agregação | learn.microsoft.com/azure/databricks/optimizations/spark-ui-guide/long-spark-stage-page | 15/11/2026 |
| Serverless não tem Spark UI; usar query profile | docs.databricks.com/aws/en/compute/serverless/limitations | set/2026 |

## Jobs

| Fato | Fonte | Conferido |
|---|---|---|
| Run if: All succeeded (padrão), At least one succeeded, None failed, All done, At least one failed, All failed | docs.databricks.com/aws/en/jobs/run-if | set/2026 |
| Metric thresholds: Warning notifica e o run continua; Timeout cancela (status Timed Out); timeout vale por tentativa | docs.databricks.com/aws/en/jobs/configure-job | out/2026 |
| File arrival trigger: monitora volume ou external location; sobrescrever arquivo existente não dispara; path sem wildcard | docs.databricks.com/aws/en/jobs/file-arrival-triggers | set/2026 |
| Histórico de runs de jobs e pipelines: 60 dias | learn.microsoft.com/azure/databricks/jobs/monitor | 19/11/2026 |
| Status de run: Queued, Pending, Running, Skipped, Succeeded, Succeeded with failures, Failed, Timed Out, Canceling, Canceled | mesma página | 19/11/2026 |

## Monitoramento (system tables)

| Fato | Fonte | Conferido |
|---|---|---|
| `system.lakeflow` tem 6 tabelas: `jobs`, `job_tasks`, `job_run_timeline`, `job_task_run_timeline`, `pipelines`, `pipeline_update_timeline` | learn.microsoft.com/azure/databricks/admin/system-tables/jobs | 19/11/2026 |
| O schema `lakeflow` antes se chamava `workflow` (conteúdo idêntico) | mesma página | 19/11/2026 |
| Acesso exige ser metastore admin e account admin, ou ter `USE` e `SELECT` nos system schemas | mesma página | 19/11/2026 |
| Registros ficam disponíveis em geral dentro de 1 hora | mesma página | 19/11/2026 |
| Retenção: a página de jobs diz 395 dias (pipelines indefinida); a página geral de system tables diz 365 dias. **Divergência entre duas páginas oficiais**: não afirmar número em prova sem conferir | admin/system-tables/jobs e admin/system-tables | 19/11/2026 |

## Governança

| Fato | Fonte | Conferido |
|---|---|---|
| Ler tabela exige `USE CATALOG` + `USE SCHEMA` + `SELECT`; só o `SELECT` não basta | learn.microsoft.com/azure/databricks/data-governance/unity-catalog/access-control/permissions-concepts | 19/11/2026 |
| `ALL PRIVILEGES` não inclui `MANAGE`, `READ METADATA`, `EXTERNAL USE SCHEMA` nem `EXTERNAL USE LOCATION` | mesma página | 19/11/2026 |
| Privilégios em catálogo ou schema valem para objetos atuais e futuros; negações herdam do mesmo jeito; grants no metastore não herdam | mesma página | 19/11/2026 |
| Ownership não herda para baixo; o dono recebe `MANAGE` nos objetos filhos | mesma página | 19/11/2026 |
| Owner não aparece em `SHOW GRANTS`; ver com `DESCRIBE CATALOG EXTENDED` | testado em set/2026 (skill databricks-lab-free-edition) | set/2026 |
| Uma tabela tem no máximo 1 row filter | data-governance/unity-catalog/filters-and-masks/manually-apply | set/2026 |
| O comando SQL `DENY` não é suportado pelo Unity Catalog (só `hive_metastore`). No UC existe política ABAC DENY (Beta), que nega só `MANAGE ACCESS CONTROL` e aparece em `SHOW EFFECTIVE POLICIES`, não em `SHOW GRANTS` | sql/language-manual/security-deny e data-governance/unity-catalog/abac/deny-policies | 15/11/2026 (corrigido: antes constava "não existe DENY") |
| `ALTER TABLE t SET MANAGED` converte external para managed; `UNSET MANAGED` desfaz em até 14 dias; exige Delta e serverless ou DBR 17.3 LTS+ | learn.microsoft.com/azure/databricks/tables/convert-to-managed | out/2026 |
| ABAC: governed tags; políticas de row filter, column mask, GRANT e DENY (Beta); DBR 16.4+ ou serverless; política não concede acesso, o principal precisa de SELECT | data-governance/unity-catalog/abac | out/2026 |

## CI/CD

| Fato | Fonte | Conferido |
|---|---|---|
| Prioridade das variáveis de bundle: `--var`, `BUNDLE_VAR_<nome>`, `variable-overrides.json`, `variables` do target, default | learn.microsoft.com/azure/databricks/dev-tools/bundles/variables | 15/11/2026 |
| `mode: development` prefixa nomes, pausa schedules e triggers, libera runs concorrentes e tira o lock | docs.databricks.com/aws/en/dev-tools/bundles/deployment-modes | set/2026 |
| `mode: production` valida `run_as`, `permissions` e caminhos que não apontam para usuário | mesma página | set/2026 |
| `bundle run` recebe a chave do recurso, não o nome final | testado em set/2026 | set/2026 |
| A variável de target em CI é `DATABRICKS_BUNDLE_TARGET` (`DATABRICKS_BUNDLE_ENV` está deprecada) | docs.databricks.com/aws/en/dev-tools/ci-cd/github | set/2026 |

## Arquivos em volumes e recuperação de jobs

| Fato | Fonte | Conferido |
|---|---|---|
| Volumes aceitam leitura e escrita com Spark, pandas, SQL e Python comum (`os`, `open`) no caminho `/Volumes/<catalogo>/<schema>/<volume>/...` | learn.microsoft.com/azure/databricks/volumes/volume-files | 19/11/2026 |
| A CLI exige `dbfs:/` antes do caminho do volume: `databricks fs cp arquivo dbfs:/Volumes/...` | mesma página e /dev-tools/cli/reference/fs-commands | 19/11/2026 |
| Em notebook e no editor SQL, só `LIST` funciona para arquivos em volume; `PUT INTO`, `GET` e `REMOVE` só por conectores e drivers | learn.microsoft.com/azure/databricks/volumes/volume-files | 19/11/2026 |
| Repair run reexecuta só as tarefas sem sucesso e as dependentes; só em job com 2 ou mais tarefas; cada tarefa reparada roda do início e não é idempotente por si | learn.microsoft.com/azure/databricks/jobs/repair-job-failures | 19/11/2026 |
| A evolução de schema com Structured Streaming pressupõe job com retries para reiniciar o ambiente e seguir | learn.microsoft.com/azure/databricks/jobs/control-flow | 19/11/2026 |
| `try_cast` devolve NULL quando o valor não converte, em vez de erro | learn.microsoft.com/azure/databricks/sql/language-manual/functions/try_cast | 19/11/2026 |

## APIs externas usadas nos cenários

| Fato | Fonte | Conferido |
|---|---|---|
| IBGE Localidades v1: `/estados/{UF}/municipios`; padrão hierárquico, `view=nivelado` deixa plano | servicodados.ibge.gov.br/api/docs/localidades | 24/10/2026 (skill ibge-apis-docs) |
| IBGE Malhas v4: `/municipios/{id}/metadados` traz centroide, área e limítrofes | servicodados.ibge.gov.br/api/docs/malhas?versao=4 | 24/10/2026 |
| IBGE: limites de taxa e termos de uso não publicados nas páginas lidas | mesmas páginas | 24/10/2026 |
| BCB SGS: `api.bcb.gov.br/dados/serie/bcdata.sgs.{codigo}/dados?formato=json&dataInicial=dd/MM/aaaa&dataFinal=dd/MM/aaaa`; janela máxima de 10 anos; filtros obrigatórios desde 26/03/2025; `/ultimos/{N}` com N até 20 | dadosabertos.bcb.gov.br (recurso json do SGS) | 19/11/2026 |
| BCB SGS: o formato da resposta (nomes dos campos) **não é publicado** na página do serviço | mesma página | 19/11/2026 |
| Open-Meteo: `api.open-meteo.com/v1/forecast`; `current` aceita qualquer variável horária; resposta com `current.time` e `current.interval`; condição atual de modelo de 15 min; `timezone` padrão GMT; uso comercial exige `apikey`; limites de requisição não constam na página | open-meteo.com/en/docs | 19/11/2026 |

## Não confirmado (não afirmar)

- Nota de corte da prova.
- Se a conta atual é Free Edition ou trial, e os limites que valem.
- Git folder com GitHub e external location própria na Free Edition.
- Sintaxe das expectations armazenadas no Unity Catalog.
- Retenção exata das system tables (duas páginas oficiais divergem).
