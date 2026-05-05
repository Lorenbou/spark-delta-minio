# Execucao

## 1. Subir os containers

```bash
docker compose up -d
```

Servicos:

- SQL Server: `localhost:1433`
- MinIO API: `http://localhost:9020`
- MinIO Console: `http://localhost:9021`

Credenciais padrao:

| Servico | Usuario | Senha |
|---|---|---|
| SQL Server | `sa` | `SqlServer@2025!` |
| MinIO | `minioadmin` | `minioadmin` |

## 2. Configurar ambiente

```bash
cp .env.example .env
uv venv
source .venv/bin/activate
uv sync
```

No Windows PowerShell:

```powershell
copy .env.example .env
uv venv
.venv\Scripts\Activate.ps1
uv sync
```

## 3. Executar notebooks

Execute nesta ordem:

1. `notebook/00_setup_sqlserver.ipynb`
2. `notebook/01_sqlserver_to_minio_csv.ipynb`
3. `notebook/02_csv_to_delta.ipynb`
4. `notebook/03_dml_delta.ipynb`

## 4. Abrir esta documentacao

```bash
mkdocs serve
```

Acesse `http://127.0.0.1:8000`.
