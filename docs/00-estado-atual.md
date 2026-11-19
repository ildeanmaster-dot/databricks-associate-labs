# Estado atual

Atualizado em **19/11/2026**. Prova: **29/11/2026**.

## Resumo

- Labs 0, 1 e 2 concluídos no workspace atual (catálogo `lab_de`).
- Lab 3 (transformação) iniciado. A parte 1 (perfil dos dados reais) foi entregue e **aguarda o resultado** das 3 células.
- Faltam os Labs 3 (resto), 5, 8, 6, 7 e 4, nesta ordem de prioridade.
- Ritmo: meio período por dia, 1 ou 2 labs por vez, sem calendário rígido.
- 5 cenários reais criados em 19/11/2026 (`cenarios/`), todos não iniciados. Ordem sugerida em [cenarios/README.md](../cenarios/README.md); se o tempo apertar, fazer só C3 e C5.
- Guia para outro agente de IA: [AGENTS.md](../AGENTS.md).

## Próximo passo exato

1. Abrir o notebook `lab3_transformacao` (SQL, Serverless). Se ele não existir, criar conforme o passo 1 de [labs/lab-03-transformacao.md](../labs/lab-03-transformacao.md).
2. Executar as células 1, 2 e 3 da **parte 1**.
3. Colar no chat: a lista de colunas (`DESCRIBE TABLE`), as 22 linhas (print serve) e a linha única do perfil.
4. Com isso o Claude escreve a **parte 2** (limpeza para `lab_de.silver.pedidos`) com os nomes reais das colunas.

## Objetos que existem no workspace (19/11/2026)

| Objeto | Tipo | Criado em | Observação |
|---|---|---|---|
| `lab_de` | catálogo | Lab 0 | default storage, sem LOCATION |
| `lab_de.bronze`, `lab_de.silver`, `lab_de.gold` | schemas | Lab 0 | junto com `default` e `information_schema` |
| `lab_de.bronze.raw` | volume | Lab 0 | 6 CSVs de pedidos em `/Volumes/lab_de/bronze/raw/` |
| `lab_de.bronze.landing` | volume | Lab 2 | pasta de chegada do Auto Loader |
| `lab_de.bronze.ctrl` | volume | Lab 2 | schema location e checkpoint do Auto Loader |
| `demo_delta` | tabela Delta | Lab 1 | histórico com versões 0 a 5 e RESTORE na versão 1 |
| `lab_de.bronze.pedidos_copy` | tabela Delta | Lab 2 | COPY INTO, 14 linhas (arquivos 01, 02 e 03) |
| `lab_de.bronze.pedidos_auto` | tabela Delta | Lab 2 | Auto Loader, 22 linhas, 1 com `_rescued_data`, 12 com `canal_venda` |

O schema exato de `demo_delta` não ficou registrado. Conferir com `SHOW TABLES IN lab_de.default` e `SHOW TABLES IN lab_de.bronze`.

## Resultados das checagens (perguntas de prova)

| Lab | Tema | Resultado |
|---|---|---|
| Lab 1 | Delta Lake | 3 perguntas; a 3 errada no primeiro teste e corrigida |
| Lab 2 | COPY INTO | 3 de 3 |
| Lab 2 | Auto Loader | 3 de 3 |

## Tópicos do Caderno de Estudos para revisar

| Depois do lab | Tópico |
|---|---|
| Lab 2 | A4 ingestão |
| Lab 3 | A3, A5 e A6 transformação |
| Lab 5 | A7 jobs |
| Lab 6 | A8 CI/CD |
| Lab 7 | A9 troubleshooting |
| Lab 8 | A2 e A10 governança |

## Pendências abertas

- Confirmar em **Manage Trial** se o workspace é conta de teste (trial) e até quando vale. O topo do workspace mostra esse botão, que a documentação da Free Edition não descreve. Se for trial, os limites da Free Edition podem não valer.
- O documento "estado-labs-databricks-2026-11-19" do projeto Databricks no Claude não pôde ser lido nesta sessão. Se ele tiver algo diferente deste arquivo, vale o mais recente e este arquivo deve ser corrigido.
- Lab 6: instalar a CLI pelo winget e fazer `databricks auth login --host <url> --profile DEFAULT` no Windows Terminal.
- Lab 8: criar 2 grupos em Settings, Identity and access.
- Labs 17 e 20 (Fase 2): testar Git folder com GitHub e external location.

## Histórico

- 24/10/2026: trilha de recomeço definida numa conta nova; nada dos labs antigos existia.
- 14/11/2026: prova remarcada para 29/11/2026; cadernos ganharam as lacunas do guia de ago/2026.
- 15/11/2026: material para NotebookLM entregue (seções 1 a 7 do guia).
- 19/11/2026: Labs 0, 1 e 2 executados; Lab 3 iniciado; esta documentação criada.
- 19/11/2026 (tarde): 5 cenários reais e o AGENTS.md criados.
