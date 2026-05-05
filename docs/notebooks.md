# Notebooks

## 00 - Setup SQL Server

Arquivo: `notebook/00_setup_sqlserver.ipynb`

Cria o banco `LojaDB`, cria as tabelas `clientes`, `produtos`, `pedidos` e `itens_pedido`, e carrega os dados da pasta `data/`.

## 01 - SQL Server para MinIO CSV

Arquivo: `notebook/01_sqlserver_to_minio_csv.ipynb`

Le as quatro tabelas do SQL Server usando Spark/JDBC e envia os arquivos CSV para o bucket `landing-zone`.

## 02 - CSV para Delta Lake

Arquivo: `notebook/02_csv_to_delta.ipynb`

Le os CSVs do bucket `landing-zone`, grava as tabelas em Delta Lake no bucket `bronze` e valida a existencia de `_delta_log`.

## 03 - DML Delta Lake

Arquivo: `notebook/03_dml_delta.ipynb`

Registra as tabelas Delta no Spark SQL e executa as operacoes:

- `INSERT`
- `UPDATE`
- `DELETE`
- `DESCRIBE HISTORY`
- time travel com `versionAsOf`

A demonstracao principal usa a tabela `produtos` e o produto de teste `id = 999`.
