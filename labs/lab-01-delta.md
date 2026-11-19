# Lab 1: Delta Lake

**Situação:** concluído em 19/11/2026.
**Seções do guia:** 1, plataforma (6%), e 6, otimização (10%).
**Caderno de Estudos:** A1 plataforma e A9 troubleshooting.

## Objetivo

Ver na prática o log de transações do Delta: versões, time travel, RESTORE, deletion vectors e a otimização automática.

## Resultados reais (tabela `demo_delta`)

| O que | Resultado |
|---|---|
| `DESCRIBE HISTORY` | versões 0 a 5 |
| Versões 3 e 5 | OPTIMIZE automático (`auto: true`), sem ninguém rodar OPTIMIZE |
| UPDATE e DELETE | usaram deletion vectors; o DELETE teve `numAddedFiles: 0` |
| Propriedade da tabela | nasceu com `clusterByAuto: true` |
| `RESTORE` para a versão 1 | a tabela ficou com 1 arquivo de 1385 bytes |
| Checagem | 3 perguntas; a 3 errada no primeiro teste e corrigida |

## Roteiro de referência

O código executado está no repositório local. Este roteiro repete as mesmas operações para quem refizer o lab.

```sql
-- Cria uma tabela Delta managed pequena para o experimento
CREATE TABLE IF NOT EXISTS lab_de.default.demo_delta (id INT, nome STRING, valor DOUBLE);
-- Versão 1: insere 3 linhas
INSERT INTO lab_de.default.demo_delta VALUES (1, 'a', 10.0), (2, 'b', 20.0), (3, 'c', 30.0);
-- Nova versão: altera uma linha (usa deletion vector em vez de reescrever o arquivo)
UPDATE lab_de.default.demo_delta SET valor = 99.0 WHERE id = 2;
-- Nova versão: apaga uma linha (numAddedFiles 0 indica deletion vector)
DELETE FROM lab_de.default.demo_delta WHERE id = 3;
-- Mostra todas as versões, a operação de cada uma e as métricas
DESCRIBE HISTORY lab_de.default.demo_delta;
-- Time travel: lê a tabela como estava na versão 1
SELECT * FROM lab_de.default.demo_delta VERSION AS OF 1;
-- Volta a tabela inteira para a versão 1 (gera uma versão nova do tipo RESTORE)
RESTORE TABLE lab_de.default.demo_delta TO VERSION AS OF 1;
-- Mostra arquivos, tamanho e formato atuais (conferir numFiles e sizeInBytes)
DESCRIBE DETAIL lab_de.default.demo_delta;
-- Mostra as propriedades (conferir clusterByAuto)
SHOW TBLPROPERTIES lab_de.default.demo_delta;
```

O schema exato usado em `demo_delta` não ficou registrado. Confira com `DESCRIBE TABLE` antes de refazer.

## Conceitos de prova

- Delta dá ACID, time travel e auditoria pelo log de transações (questão 2 do guia).
- VACUUM: limite padrão de 7 dias; arquivos de log ficam 30 dias. VACUUM remove a capacidade de voltar além do limite.
- `RESTORE` cria uma versão nova; não apaga o histórico.
- Deletion vectors aceleram UPDATE, DELETE e MERGE marcando linhas como apagadas.
- Predictive optimization roda OPTIMIZE, VACUUM e ANALYZE sozinha em tabelas managed; não agende OPTIMIZE manual quando ela está ligada.
