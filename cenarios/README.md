# Cenários reais (labs de cotidiano)

Cinco laboratórios que simulam situações de trabalho de um engenheiro de dados: consumir API, receber dado em fluxo contínuo, corrigir dado revisado pelo fornecedor, montar pipeline quase em tempo real e apagar um incêndio de produção com regra de compliance.

Criados em **19/11/2026**. Seguem as mesmas regras dos labs principais ([docs/01-regras-didaticas.md](../docs/01-regras-didaticas.md)).

## Mapa

| Cenário | Situação de negócio | Fonte | Técnicas | Seções do guia |
|---|---|---|---|---|
| [C1](cenario-01-api-ibge-municipios.md) | Time comercial precisa da tabela de municípios do Pará com mesorregião e região imediata para definir territórios | API REST do IBGE (Localidades) | REST em notebook, JSON aninhado, achatar struct, Job agendado | S2, S3, S4 |
| [C2](cenario-02-api-bcb-cambio-merge.md) | Financeiro usa o dólar do dia e o Banco Central às vezes revisa valores já publicados | API SGS do Banco Central | carga incremental com janela, MERGE (upsert), time travel para auditoria | S2, S3, S1 |
| [C3](cenario-03-streaming-eventos-pedidos.md) | E-commerce manda eventos de pedido o tempo todo, com duplicados e fora de ordem | Produtor simulado em notebook (arquivos em volume) | Auto Loader, dedup, MERGE com regra de ordem, file arrival trigger | S2, S3, S4 |
| [C4](cenario-04-clima-quase-tempo-real.md) | Operação de campo em Marabá quer a condição do tempo atualizada várias vezes ao dia | API Open-Meteo + IBGE Malhas (coordenadas) | Job agendado, pipeline declarativo, expectations, materialized view, thresholds | S3, S4, S6 |
| [C5](cenario-05-incidente-schema-drift-compliance.md) | Segunda-feira: o fornecedor mudou o formato dos eventos, o job falhou e o jurídico pediu para esconder o e-mail do cliente | Produtor do C3 em versão 2 | diagnóstico pelo run history, rescued data, correção e reprocesso, column mask, row filter | S6, S7, S2 |

## Ordem e momento

| Quando | Cenário | Por quê |
|---|---|---|
| Depois do Lab 3 | C1 e C2 | Usam limpeza, MERGE e agregação do Lab 3 |
| Depois do Lab 5 | C3 | Usa Job com file arrival trigger |
| Depois do Lab 4 (ou junto) | C4 | Usa pipeline declarativo (1 pipeline ativo por tipo na Free Edition) |
| Depois do Lab 8 | C5 | Usa grupos, mask e row filter |

Se o tempo até 29/11/2026 apertar, faça só C3 e C5: cobrem ingestão, transformação, Jobs, troubleshooting e governança.

## Objetos novos que os cenários criam

| Objeto | Tipo | Cenário |
|---|---|---|
| `lab_de.bronze.api_raw` | volume (respostas brutas das APIs) | C1, C2, C4 |
| `lab_de.bronze.eventos` | volume (chegada dos eventos) | C3, C5 |
| `lab_de.silver.dim_municipio_pa` | tabela | C1 |
| `lab_de.bronze.cambio_raw`, `lab_de.silver.cambio_dolar` | tabelas | C2 |
| `lab_de.bronze.eventos_pedido`, `lab_de.silver.pedido_estado` | tabelas | C3, C5 |
| pipeline `clima_maraba` (`bronze_clima`, `silver_clima`, `gold_clima_hora`) | streaming tables e materialized view | C4 |

## Antes de qualquer cenário com API: teste de internet de saída

A Free Edition restringe a saída para a internet. A conta atual mostra o botão Manage Trial e pode ter outra regra. O passo 0 de C1, C2 e C4 testa isso. Se falhar, cada cenário traz o **plano B**: buscar o JSON no seu Windows com PowerShell e subir para o volume com a CLI (`databricks fs cp ... dbfs:/Volumes/...`).

Fonte do plano B: learn.microsoft.com/azure/databricks/volumes/volume-files (a CLI exige o prefixo `dbfs:/` nos caminhos de volume; conferido em 19/11/2026).
