# Guia da prova (Data Engineer Associate)

Guia oficial vigente: publicado em 4/mai/2026, **atualizado em 6/ago/2026**. Reconferido em 14/11/2026. O próprio guia recomenda reconferir 2 semanas antes da prova.

Fonte: https://www.databricks.com/learn/certification/data-engineer-associate e o PDF `Databricks-Certified-Data-Engineer-Associate-Exam-Guide-August-2026.pdf`.

## Formato

| Item | Valor |
|---|---|
| Questões pontuadas | 45 (mais até 10 não pontuadas) |
| Duração | 90 min |
| Idiomas | inglês, japonês, português (BR), coreano |
| Formato | múltipla escolha, online ou test center, sem material de apoio |
| Taxa | USD 200 + impostos |
| Validade | 2 anos |
| Nota de corte | **não publicada** (política oficial) |
| Retake | espera de 14 dias, preço cheio a cada tentativa |
| Simulado oficial | **não existe** |

## Blueprint (seções e pesos)

| # | Seção | Peso | Labs |
|---|---|---|---|
| 1 | Databricks Intelligence Platform | 6% | 0, 1 |
| 2 | Data Ingestion & Loading | **21%** | 2 |
| 3 | Data Transformation & Modeling | **22%** | 3, 4 |
| 4 | Working with Lakeflow Jobs | 16% | 5 |
| 5 | Implementing CI/CD | 10% | 6 |
| 6 | Troubleshooting, Monitoring & Optimization | 10% | 7 |
| 7 | Governance & Security | 15% | 8 |

Ingestão e transformação somam 43%.

## Objetivos por seção

1. **Plataforma:** componentes (arquitetura, Delta Lake, Unity Catalog); compute (características, limites, custo) e o melhor para cada carga.
2. **Ingestão:** batch, streaming e incremental; arquivo local e conectores Lakeflow Connect; COPY INTO incremental; Auto Loader (directory listing ou file notification) com enforcement e evolução de schema; Lakeflow Connect para fontes corporativas; JDBC, ODBC ou REST em notebook orquestrado por Jobs; escolher a ferramenta por volume, frequência, tipo e governança; JSON aninhado e dados não estruturados para Delta.
3. **Transformação:** limpar bronze (nulos, tipos) para silver; joins (inner, left, broadcast, multi-chave, cross, union, union all); adicionar, remover, dividir e renomear colunas, filtrar, reestruturar, explode; deduplicar e agregar (count, approx count distinct, mean, summary); ajustar parâmetros Spark e re-medir; objetos gold (MV, view, streaming table, tabela); checagens de qualidade.
4. **Jobs:** retries, condicionais, loops; tarefas notebook, SQL query, dashboard e pipeline em DAG; triggers scheduled, file arrival e table update; time-based x data-driven.
5. **CI/CD:** Git folders (branch, commit, push, PR); configuração por ambiente com variáveis e overrides de bundles; deploy de Jobs e pipelines por bundle; CLI em CI/CD.
6. **Troubleshooting:** duração no run history contra baseline; saúde do pipeline na UI de Jobs; skew, shuffle e spill na Spark UI; liquid clustering e predictive optimization; falhas de startup, conflito de biblioteca e OOM.
7. **Governança:** managed x external (criar, modificar, apagar, converter); GRANT, REVOKE e DENY; column mask e row filter por grupo; políticas ABAC.

## As 10 questões de exemplo do guia (tema e resposta)

1. Skew na Spark UI com AQE ligado: salting da chave quente.
2. Rollback, auditoria e fonte única para IA e BI: Delta Lake (ACID, time travel) com Unity Catalog.
3. Entrega de audit logs: JSON, em geral abaixo de 15 min, e entregas posteriores podem sobrescrever arquivos.
4. Analistas com SQL ad hoc em tabelas curadas: SQL warehouse serverless com autoscaling.
5. Deploy modular, versionado e orquestrado: bundles em Git promovidos por CI/CD.
6. Senha fora de YAML e código: variável de bundle fornecida por `BUNDLE_VAR_`.
7. Só o financeiro vê o cartão completo: column mask com função por grupo.
8. Desfazer conversão external para managed: `ALTER TABLE ... UNSET MANAGED`.
9. Task estoura em 45 min e precisa de até 60: subir o threshold de duração da task.
10. Processar arquivos logo após a chegada, sem compute ocioso: file arrival trigger.

## Renomeações que caem na prova

| Nome antigo | Nome atual |
|---|---|
| Delta Live Tables (DLT) | Lakeflow (Spark) Declarative Pipelines |
| Workflows | Lakeflow Jobs |
| APPLY CHANGES | AUTO CDC (mesma sintaxe) |
| Databricks Asset Bundles | Declarative Automation Bundles (16/mar/2026; comando continua `databricks bundle`) |
| Repos | Git folders |
| partitioning e ZORDER | Liquid clustering (`CLUSTER BY`, `CLUSTER BY AUTO`) |
| Delta Sharing | OpenSharing (10/jun/2026) |
| `import dlt` | `from pyspark import pipelines as dp` |
| `@dlt.view` | `@dp.temporary_view` |

O guia do Associate escreve "Lakeflow Spark Declarative Pipeline". A documentação escreve "Lakeflow pipelines". São a mesma coisa.
