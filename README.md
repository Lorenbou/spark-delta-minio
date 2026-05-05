# Trabalho 2 - Apache Spark com MinIO, SQL Server e Delta Lake

Este repositorio implementa o Trabalho 2 da disciplina de Engenharia de Dados. O objetivo e construir um pipeline local usando SQL Server, MinIO, Apache Spark e Delta Lake.

O fluxo parte de quatro tabelas relacionais de uma base de vendas chamada `LojaDB`, extrai os dados para CSV no bucket `landing-zone` do MinIO, converte os arquivos para Delta Lake no bucket `bronze` e demonstra operacoes DML com historico e time travel.



Acesse a documentação online MkDocs em [https://lorenbou.github.io/spark-delta-minio/](https://lorenbou.github.io/spark-delta-minio/)

## Arquitetura do pipeline

```text
data/*.csv
   |
   v
SQL Server 2025 / LojaDB
   |
   | Notebook 01 - Spark JDBC
   v
MinIO / landing-zone
   |-- clientes.csv
   |-- produtos.csv
   |-- pedidos.csv
   `-- itens_pedido.csv
   |
   | Notebook 02 - Spark + Delta Lake
   v
MinIO / bronze
   |-- clientes/
   |   `-- _delta_log/
   |-- produtos/
   |   `-- _delta_log/
   |-- pedidos/
   |   `-- _delta_log/
   `-- itens_pedido/
       `-- _delta_log/
   |
   | Notebook 03 - DML, History e Time Travel
   v
Delta Lake com transacoes, historico e versionamento
```

## Tecnologias usadas

- Apache Spark 3.5.3
- Delta Lake 3.2.0
- MinIO como object storage compativel com S3
- SQL Server 2025 Developer
- Docker Compose
- Python 3.11
- JupyterLab
- PySpark
- boto3
- pyodbc
- MkDocs

## Estrutura do projeto

```text
spark-delta-minio-sqlserver/
|-- data/
|   |-- clientes.csv
|   |-- produtos.csv
|   |-- pedidos.csv
|   `-- itens_pedido.csv
|-- docs/
|   |-- index.md
|   |-- arquitetura.md
|   |-- execucao.md
|   |-- notebooks.md
|   `-- tabelas-gerenciadas.md
|-- notebook/
|   |-- 00_setup_sqlserver.ipynb
|   |-- 01_sqlserver_to_minio_csv.ipynb
|   |-- 02_csv_to_delta.ipynb
|   `-- 03_dml_delta.ipynb
|-- .env.example
|-- .python-version
|-- docker-compose.yml
|-- mkdocs.yml
|-- pyproject.toml
`-- README.md
```

## Como executar

### 1. Subir SQL Server e MinIO

```bash
docker compose up -d
```

Servicos principais:

| Servico | URL/porta | Usuario | Senha |
|---|---:|---|---|
| SQL Server | `localhost:1433` | `sa` | `SqlServer@2025!` |
| MinIO API | `http://localhost:9020` | `minioadmin` | `minioadmin` |
| MinIO Console | `http://localhost:9021` | `minioadmin` | `minioadmin` |

### 2. Criar arquivo `.env`

```bash
cp .env.example .env
```

O arquivo deve usar:

```env
DB_DATABASE=LojaDB
MINIO_LANDING_BUCKET=landing-zone
MINIO_BRONZE_BUCKET=bronze
```

### 3. Preparar ambiente Python

```bash
uv venv
source .venv/bin/activate
uv sync
```

No Windows PowerShell:

```powershell
uv venv
.venv\Scripts\Activate.ps1
uv sync
```

### 4. Executar os notebooks em ordem

1. `notebook/00_setup_sqlserver.ipynb`
2. `notebook/01_sqlserver_to_minio_csv.ipynb`
3. `notebook/02_csv_to_delta.ipynb`
4. `notebook/03_dml_delta.ipynb`

## O que cada notebook faz

| Notebook | Funcao |
|---|---|
| `00_setup_sqlserver.ipynb` | Cria o banco `LojaDB`, cria as tabelas relacionais e carrega os CSVs locais. |
| `01_sqlserver_to_minio_csv.ipynb` | Le as quatro tabelas do SQL Server com Spark/JDBC e envia CSVs para o bucket `landing-zone`. |
| `02_csv_to_delta.ipynb` | Le os CSVs do `landing-zone`, grava cada tabela em Delta Lake no bucket `bronze` e valida `_delta_log`. |
| `03_dml_delta.ipynb` | Registra as tabelas Delta, executa `INSERT`, `UPDATE`, `DELETE`, mostra `DESCRIBE HISTORY` e faz `TIME TRAVEL`. |

## Evidencias esperadas no MinIO

Depois do notebook 01, o bucket `landing-zone` deve conter:

```text
landing-zone/
|-- clientes.csv
|-- produtos.csv
|-- pedidos.csv
`-- itens_pedido.csv
```

Depois do notebook 02, o bucket `bronze` deve conter tabelas Delta:

```text
bronze/
|-- clientes/
|   |-- _delta_log/
|   `-- part-...
|-- produtos/
|   |-- _delta_log/
|   `-- part-...
|-- pedidos/
|   |-- _delta_log/
|   `-- part-...
`-- itens_pedido/
    |-- _delta_log/
    `-- part-...
```

A pasta `_delta_log` e a consulta `DESCRIBE HISTORY` sao as principais evidencias de que os dados foram gravados em Delta Lake.

No notebook 03, a tabela `produtos` deve mostrar:

1. Produto `id = 999` inserido.
2. Produto `id = 999` atualizado.
3. Produto `id = 999` deletado.
4. Historico Delta com operacoes `WRITE`, `UPDATE` e `DELETE`.
5. Time travel mostrando versoes anteriores da tabela.

## Tabelas gerenciadas e nao gerenciadas

Uma tabela gerenciada e controlada pelo metastore do Spark/Hive. Nesse caso, o sistema gerencia tanto os metadados quanto o local fisico dos dados.

Uma tabela nao gerenciada, tambem chamada de externa, usa um caminho definido explicitamente pelo usuario. O metastore conhece a tabela, mas os dados continuam em um local externo, como um bucket S3/MinIO.

Neste trabalho, as tabelas Delta do bucket `bronze` sao registradas assim:

```sql
CREATE TABLE produtos
USING delta
LOCATION 's3a://bronze/produtos'
```

Como o caminho `LOCATION` e definido manualmente no MinIO, essas tabelas sao tratadas como externas/nao gerenciadas. Isso combina com a proposta do trabalho, pois os dados ficam no object storage e o Spark acessa as tabelas Delta diretamente pelo caminho.

## Documentacao MkDocs

Para abrir a documentacao curta:

```bash
mkdocs serve
```

Depois acesse:

```text
http://127.0.0.1:8000
```
