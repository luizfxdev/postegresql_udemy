# 10 — JOIN e UNION

| | Junta | Resultado |
|-|-------|-----------|
| `JOIN` | **colunas** de tabelas diferentes (lado a lado) | Mais colunas |
| `UNION` | **linhas** de consultas diferentes (empilhadas) | Mais linhas |

---

## JOIN — ligando tabelas

**Analogia:** o pedido só guarda o `cliente_id` (o número da etiqueta). O `JOIN` vai ao corredor
"clientes" buscar o nome correspondente.

```sql
SELECT p.id, c.nome AS cliente, p.frete
FROM pedidos p
JOIN clientes c ON c.id = p.cliente_id;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `FROM pedidos p` | De pedidos (apelido `p`) | Tabela principal |
| `JOIN clientes c` | Juntar com clientes (apelido `c`) | Traz a segunda tabela |
| `ON c.id = p.cliente_id` | Em / com base em | Regra de ligação: o id do cliente = cliente do pedido |
| `AS cliente` | Como "cliente" | Nome da coluna no resultado |

### Tipos de JOIN

| Tipo | Português | Traz | Uso típico |
|------|-----------|------|-----------|
| `INNER JOIN` (ou só `JOIN`) | Junção interna | Só o que casa nos dois lados | Pedidos com cliente |
| `LEFT JOIN` | Junção à esquerda | Tudo da esquerda + o que casar | **Todos** os clientes, mesmo sem pedido |
| `RIGHT JOIN` | Junção à direita | Tudo da direita + o que casar | Inverso do LEFT |
| `FULL JOIN` | Junção completa | Tudo dos dois lados | Auditoria / conciliação |

```sql
-- Clientes que NUNCA fizeram pedido
SELECT c.nome
FROM clientes c
LEFT JOIN pedidos p ON p.cliente_id = c.id
WHERE p.id IS NULL;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `LEFT JOIN` | Junção à esquerda | Mantém **todos** os clientes, mesmo sem pedido (colunas do pedido vêm `NULL`) |
| `ON` | Em / com base em | Regra de ligação |
| `WHERE p.id IS NULL` | Onde o pedido é nulo | Sobram só os clientes sem nenhum pedido |

```sql
-- Produtos por categoria, versão "vírgula" (equivale ao INNER JOIN)
SELECT c.nome, COUNT(p.id)
FROM produtos p, categorias c
WHERE p.categoria_id = c.id
GROUP BY c.nome;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `FROM a, b` | De a e b | Lista duas tabelas (gera todas as combinações) |
| `WHERE p.categoria_id = c.id` | Onde as chaves coincidem | Filtra só as combinações corretas |
| `GROUP BY` / `COUNT` | Agrupar por / Contar | Total de produtos por categoria |

```sql
-- Mesma consulta, versão recomendada
SELECT c.nome, COUNT(p.id) AS total
FROM produtos p
JOIN categorias c ON c.id = p.categoria_id
GROUP BY c.nome;
```

> Prefira `JOIN ... ON`: é mais legível e evita o produto cartesiano acidental
> (esquecer o `WHERE` na versão "vírgula" multiplica todas as linhas de uma tabela pelas da outra).

---

## UNION — empilhando resultados

Precisam ter o mesmo número e tipo de colunas.

```sql
SELECT nome, 'Cliente' AS tipo FROM clientes
UNION
SELECT nome, 'Fornecedor' AS tipo FROM fornecedores
ORDER BY nome;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `UNION` | União | Empilha as linhas dos dois `SELECT` e **remove duplicados** |
| `'Cliente' AS tipo` | Texto fixo como "tipo" | Cria uma coluna para identificar a origem de cada linha |
| `ORDER BY nome` | Ordenar por nome | Vale para o **resultado final** da união |

```sql
SELECT cidade FROM clientes
UNION
SELECT cidade FROM fornecedores;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `UNION` | União | Lista as cidades com que a operação se relaciona, **sem repetir** |

```sql
SELECT pais FROM clientes
UNION ALL
SELECT pais FROM fornecedores;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `UNION ALL` | União de todos | Empilha **mantendo duplicados** (mais rápido; bom para contar ocorrências) |

```sql
SELECT nome, 'Barato' AS faixa
FROM produtos
WHERE preco < 20

UNION

SELECT nome, 'Caro' AS faixa
FROM produtos
WHERE preco >= 100;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `WHERE preco < 20` | Onde preço menor que 20 | Primeira "pilha": produtos baratos |
| `UNION` | União | Junta com a segunda pilha |
| `WHERE preco >= 100` | Onde preço maior ou igual a 100 | Segunda pilha: produtos caros |

```sql
SELECT nome FROM produtos WHERE descontinuado = false
UNION
SELECT nome FROM produtos WHERE descontinuado = true
ORDER BY nome;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `UNION` | União | Junta ativos e descontinuados |
| `ORDER BY` | Ordenar por | Ordena a lista final inteira |

| | Comportamento |
|-|---------------|
| `UNION` | Junta e **remove duplicados** |
| `UNION ALL` | Junta e **mantém** duplicados (mais rápido) |

`ORDER BY` só vale **no final** de todo o `UNION`.

---

[← Números e agregações](09-numeros-e-agregacoes.md) · [Índice](README.md) · [Próximo: DML →](11-dml.md)
