# Trabalho 2 - Spark, MinIO, SQL Server e Delta Lake

Este projeto implementa um pipeline local de engenharia de dados usando SQL Server, MinIO, Apache Spark e Delta Lake.

O objetivo e extrair quatro tabelas relacionais de uma base chamada `LojaDB`, gravar os dados no MinIO em CSV, converter para Delta Lake e demonstrar operacoes transacionais.

## Dataset

As tabelas usadas no trabalho sao:

- `clientes`
- `produtos`
- `pedidos`
- `itens_pedido`

## Fluxo geral

```text
SQL Server / LojaDB
  -> MinIO / landing-zone em CSV
  -> MinIO / bronze em Delta Lake
  -> DML, History e Time Travel
```

## Entregas tecnicas

- Extracao de dados relacionais para object storage.
- Conversao de CSV para Delta Lake.
- Uso de `INSERT`, `UPDATE` e `DELETE` em tabela Delta.
- Uso de `DESCRIBE HISTORY`.
- Uso de time travel com `versionAsOf`.
