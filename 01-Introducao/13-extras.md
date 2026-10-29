# 13 — Extras que valem conhecer

> `CREATE INDEX`, `CREATE VIEW`, `ALTER` e `DROP` estão no [tópico 04 — DDL](04-ddl.md).

## Subconsulta (SELECT dentro de SELECT)

```sql
-- Produtos mais caros que a média
SELECT nome, preco
FROM produtos
WHERE preco > (SELECT AVG(preco) FROM produtos);
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `SELECT nome, preco` | Selecionar nome, preço | Colunas da consulta externa |
| `FROM produtos` | De produtos | Tabela de origem |
| `WHERE preco > (...)` | Onde preço maior que (...) | Compara com o resultado da consulta interna |
| `(SELECT AVG(preco) ...)` | Subconsulta | Roda primeiro e devolve a média, que a consulta externa usa |
| `AVG` | Média | Preço médio |

## EXPLAIN — "como o banco vai buscar?"

```sql
EXPLAIN ANALYZE SELECT * FROM pedidos WHERE cliente_id = 7;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `EXPLAIN` | Explicar | Mostra o **plano** de execução da consulta |
| `ANALYZE` | Analisar | **Executa de verdade** e mede o tempo real |
| `WHERE cliente_id = 7` | Onde o cliente é 7 | Filtro que queremos avaliar |

Se aparecer `Seq Scan` numa tabela enorme, talvez falte um [índice](04-ddl.md).

## Backup rápido

```bash
pg_dump -U postgres logistica > backup.sql        # exporta
psql -U postgres -d logistica -f backup.sql       # restaura
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `pg_dump` | Despejar banco | Gera um script `.sql` com estrutura e dados |
| `>` | Redirecionar para arquivo | Salva a saída em `backup.sql` |
| `psql -f` | Rodar arquivo | Reexecuta o script e restaura o banco |

---

[← Transações](12-transacoes.md) · [Índice](README.md) · [Próximo: Ponte com o curso →](14-northwind-curso.md)
