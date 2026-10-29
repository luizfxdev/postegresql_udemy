# 14 — Ponte com o curso (banco Northwind)

O Northwind é um banco de uma distribuidora: ótimo para praticar. Equivalência com o nosso cenário:

| Northwind (curso) | Logística (este guia) |
|-------------------|-----------------------|
| `customers` | `clientes` |
| `products` | `produtos` |
| `orders` | `pedidos` |
| `order_details` | `itens_pedido` |
| `employees` | `motoristas` |
| `suppliers` | `fornecedores` |
| `categories` | `categorias` |
| `freight` | `frete` |
| `ship_country` | `pais_destino` |
| `shipped_date` | `data_entrega` |
| `unit_price` | `preco` / `preco_unit` |
| `discontinued` (0/1) | `descontinuado` (boolean) |

## Carregar o Northwind

```bash
su - postgres
psql
```

```sql
CREATE DATABASE northwind;
\q
```

```bash
wget https://raw.githubusercontent.com/Mazuco/PostgreSQL/refs/heads/master/northwind.sql
psql -U postgres -d northwind -f northwind.sql
```

```sql
\c northwind
\dt
```

Referência do banco: <https://github.com/pthom/northwind_psql>

## Mapa das aulas

| Aula | Tema | Tópico deste guia |
|------|------|-------------------|
| 2 | Popular o banco | [02](02-instalacao-e-psql.md) e [14](14-northwind-curso.md) |
| 3 | SELECT básico, `\x`, WHERE | [05](05-select-e-filtros.md) |
| 4 | Alias, CONCAT, CASE | [05](05-select-e-filtros.md), [07](07-transformando-resultados.md) e [08](08-strings.md) |
| 5 | LIMIT, RANDOM, NULL, COALESCE | [05](05-select-e-filtros.md), [06](06-order-by-e-limit.md) e [07](07-transformando-resultados.md) |
| 6 | ORDER BY | [06](06-order-by-e-limit.md) |
| 7 | UNION | [10](10-join-e-union.md) |
| 9 | Strings | [08](08-strings.md) |
| 10–11 | Números, agregações, GROUP BY | [09](09-numeros-e-agregacoes.md) |
| 12 | INSERT | [11 — DML](11-dml.md) |
| 13 | UPDATE | [11 — DML](11-dml.md) |
| 14 | DELETE | [11 — DML](11-dml.md) |

---

[← Extras](13-extras.md) · [Índice](README.md) · [Próximo: Erros comuns e cola →](15-erros-e-cola.md)
