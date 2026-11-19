# Lab 0: Ambiente

**Situação:** concluído em 19/11/2026.
**Seção do guia:** 1, Databricks Intelligence Platform (6%).
**Caderno de Estudos:** A1 plataforma.

## Objetivo

Criar a estrutura do medalhão no Unity Catalog: um catálogo do projeto, um schema por camada e um volume para receber os arquivos.

## O que foi provado no ambiente

Os schemas `bronze`, `default`, `gold`, `information_schema` e `silver` existem em `lab_de`, e o volume `lab_de.bronze.raw` recebeu os 6 CSVs.

## Código de referência

O script executado está no repositório local (`databricks-de-lab/sql/00-setup`). O bloco abaixo reproduz o mesmo resultado para quem montar o ambiente de novo.

```sql
-- Cria o catálogo do projeto; sem LOCATION ele usa o default storage do workspace
CREATE CATALOG IF NOT EXISTS lab_de;
-- Camada bronze: dado cru, como chegou
CREATE SCHEMA IF NOT EXISTS lab_de.bronze;
-- Camada silver: dado limpo e tipado
CREATE SCHEMA IF NOT EXISTS lab_de.silver;
-- Camada gold: dado modelado para consumo
CREATE SCHEMA IF NOT EXISTS lab_de.gold;
-- Volume gerenciado para os arquivos de entrada (landing zone)
CREATE VOLUME IF NOT EXISTS lab_de.bronze.raw;
-- Confere os schemas criados (esperado: bronze, default, gold, information_schema, silver)
SHOW SCHEMAS IN lab_de;
```

Upload dos CSVs: Catalog, `lab_de`, `bronze`, `raw`, botão **Upload to this volume**. Interface porque o upload de arquivo local para volume não tem comando SQL.

```sql
-- Lista os arquivos do volume para conferir o upload (esperado: 6 arquivos pedidos_2026_*)
LIST '/Volumes/lab_de/bronze/raw/';
```

## Conceitos de prova

- Namespace de 3 níveis: `catalogo.schema.objeto`.
- Volume é o lugar governado para arquivos não tabulares; caminho `/Volumes/<catalogo>/<schema>/<volume>/`.
- Tabela managed é o padrão e o recomendado.
- O dono de um objeto não aparece em `SHOW GRANTS`; use `DESCRIBE CATALOG EXTENDED`.
