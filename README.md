# Labs Databricks Data Engineer Associate

Documentação dos laboratórios de preparação de Ildean Freitas para a prova **Databricks Certified Data Engineer Associate**, marcada para **29/11/2026**.

Este repositório é a fonte para continuar o estudo em qualquer computador. Abra primeiro o arquivo de estado e depois o lab da vez.

Última atualização: **19/11/2026**.

## Por onde começar

1. Leia [docs/00-estado-atual.md](docs/00-estado-atual.md): o que já foi feito e qual é o próximo passo.
2. Abra um chat novo com o Claude e cole o prompt de [PROMPT-RETOMADA.md](PROMPT-RETOMADA.md).
3. Siga o lab da vez em [labs/](labs/), uma parte por vez, colando o resultado de cada parte antes de pedir a próxima.

## Situação dos labs (19/11/2026)

| Lab | Tema | Seção do guia | Peso | Situação |
|---|---|---|---|---|
| [Lab 0](labs/lab-00-ambiente.md) | Ambiente (catálogo, schemas, volume) | S1 | 6% | Concluído |
| [Lab 1](labs/lab-01-delta.md) | Delta Lake | S1 e S6 | 6% e 10% | Concluído |
| [Lab 2](labs/lab-02-ingestao.md) | Ingestão (COPY INTO e Auto Loader) | S2 | 21% | Concluído |
| [Lab 3](labs/lab-03-transformacao.md) | Transformação bronze para silver e gold | S3 | 22% | Em andamento (parte 1 entregue) |
| [Lab 5](labs/lab-05-lakeflow-jobs.md) | Lakeflow Jobs | S4 | 16% | Não iniciado |
| [Lab 8](labs/lab-08-governanca.md) | Governança e segurança | S7 | 15% | Não iniciado |
| [Lab 6](labs/lab-06-bundles-cli.md) | Bundles e CLI | S5 | 10% | Não iniciado |
| [Lab 7](labs/lab-07-monitoring.md) | Monitoramento e otimização | S6 | 10% | Não iniciado |
| [Lab 4](labs/lab-04-pipeline-declarativo.md) | Pipeline declarativo (Lakeflow) | S3 e S4 | sem peso próprio | Não iniciado |
| [Fase 2](labs/fase-2-labs-10-a-23.md) | Labs 10 a 23 | várias | - | Só se sobrar tempo |

A tabela está na ordem de prioridade (peso de cada seção no guia de ago/2026).

## Cenários reais (criados em 19/11/2026)

Labs de cotidiano para treinar situações de trabalho. Detalhe e ordem em [cenarios/README.md](cenarios/README.md).

| Cenário | Situação | Situação do lab |
|---|---|---|
| [C1](cenarios/cenario-01-api-ibge-municipios.md) | API REST do IBGE, JSON aninhado, Job semanal | Não iniciado |
| [C2](cenarios/cenario-02-api-bcb-cambio-merge.md) | API do Banco Central, carga incremental, MERGE, auditoria | Não iniciado |
| [C3](cenarios/cenario-03-streaming-eventos-pedidos.md) | Eventos em fluxo contínuo, duplicados e fora de ordem | Não iniciado |
| [C4](cenarios/cenario-04-clima-quase-tempo-real.md) | Clima quase em tempo real, pipeline declarativo, thresholds | Não iniciado |
| [C5](cenarios/cenario-05-incidente-schema-drift-compliance.md) | Incidente de schema, reprocesso, column mask e row filter | Não iniciado |

## Para agentes de IA

Qualquer agente que for conduzir os labs deve ler primeiro o [AGENTS.md](AGENTS.md).

## Conteúdo

| Arquivo | Para que serve |
|---|---|
| [docs/00-estado-atual.md](docs/00-estado-atual.md) | Estado do estudo, resultados das checagens, próximo passo |
| [docs/01-regras-didaticas.md](docs/01-regras-didaticas.md) | Como cada lab é conduzido (Ação, Por quê, O que acontece, Conferência) |
| [docs/02-ambiente.md](docs/02-ambiente.md) | Workspace, objetos criados, limites e pendências de ambiente |
| [docs/03-guia-da-prova.md](docs/03-guia-da-prova.md) | Blueprint oficial, formato, as 10 questões de exemplo, renomeações |
| [docs/04-fatos-oficiais.md](docs/04-fatos-oficiais.md) | Fatos técnicos conferidos na documentação oficial, com fonte e data |
| [docs/05-erros-e-licoes.md](docs/05-erros-e-licoes.md) | Erros reais vistos nos labs e o conceito de prova que cada um treina |
| [docs/06-plano-ate-a-prova.md](docs/06-plano-ate-a-prova.md) | Plano flexível até 29/11/2026 |
| [PROMPT-RETOMADA.md](PROMPT-RETOMADA.md) | Prompt pronto para abrir um chat novo e continuar |
| [AGENTS.md](AGENTS.md) | Guia completo para outro agente de IA conduzir os labs |
| [cenarios/README.md](cenarios/README.md) | Índice dos 5 cenários reais |

## Regras do projeto

- Só documentação oficial (docs.databricks.com, learn.microsoft.com/azure/databricks e os guias oficiais da certificação). Nada por inferência. O que não foi confirmado fica marcado como **não confirmado**.
- Ao corrigir uma afirmação anterior, dizer o que mudou e a fonte.
- Script primeiro (SQL, Python, CLI). Interface só quando não houver script.
- Todo código de notebook tem comentário em cada linha.
- Perguntas de checagem só de múltipla escolha ou verdadeiro/falso, respondidas antes da correção.

## Material relacionado (fora deste repositório)

- Caderno de Estudos (artefato no Claude): https://claude.ai/artifact/M29Pqb7wwChTQ7NPwDRu9p
- Caderno de Simulados (artefato no Claude): https://claude.ai/artifact/RbrqpYafRfVDkRskVW5Gii
- Repositório local dos labs: `D:\Portifolio\Databricks\databricks-de-lab` (dados em `data/`, cópia em `D:\Treinamento\databricks\data`)
