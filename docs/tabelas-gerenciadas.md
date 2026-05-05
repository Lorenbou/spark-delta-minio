# Tabelas gerenciadas e nao gerenciadas

## Tabelas gerenciadas

Uma tabela gerenciada e controlada pelo metastore do Spark/Hive. O sistema gerencia os metadados e tambem o local fisico onde os dados sao armazenados.

Esse tipo de tabela e comum quando o ambiente possui um catalogo/metastore centralizado e o Spark controla completamente o ciclo de vida da tabela.

## Tabelas nao gerenciadas

Uma tabela nao gerenciada, tambem chamada de externa, possui um caminho fisico definido pelo usuario.

O metastore registra os metadados, mas os dados ficam em um local externo, como um bucket S3 ou MinIO.

Exemplo usado no trabalho:

```sql
CREATE TABLE produtos
USING delta
LOCATION 's3a://bronze/produtos'
```

Nesse caso, a tabela aponta diretamente para o caminho Delta no MinIO.

## Aplicacao neste trabalho

As tabelas do bucket `bronze` sao tratadas como externas porque o caminho e informado explicitamente com `LOCATION`.

Isso e adequado para uma arquitetura lakehouse, pois os dados ficam no object storage e podem ser acessados pelo Spark usando o formato Delta Lake.

A vantagem do Delta Lake nesse contexto e adicionar transacoes, historico e time travel sobre arquivos armazenados no MinIO.
