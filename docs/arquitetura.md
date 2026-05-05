# Arquitetura

O pipeline possui tres camadas principais.

## Origem

O SQL Server 2025 armazena o banco `LojaDB` com quatro tabelas:

- `clientes`
- `produtos`
- `pedidos`
- `itens_pedido`

Essas tabelas sao criadas e carregadas a partir dos arquivos CSV da pasta `data/`.

## Landing Zone

O notebook 01 le as tabelas do SQL Server com Spark/JDBC e grava um arquivo CSV por tabela no bucket `landing-zone`.

```text
landing-zone/
|-- clientes.csv
|-- produtos.csv
|-- pedidos.csv
`-- itens_pedido.csv
```

## Bronze

O notebook 02 le os CSVs do `landing-zone` e grava cada tabela em formato Delta Lake no bucket `bronze`.

```text
bronze/
|-- clientes/
|   `-- _delta_log/
|-- produtos/
|   `-- _delta_log/
|-- pedidos/
|   `-- _delta_log/
`-- itens_pedido/
    `-- _delta_log/
```

A pasta `_delta_log` registra as transacoes Delta Lake e permite historico e time travel.
