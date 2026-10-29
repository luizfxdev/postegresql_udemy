# 06 — ORDER BY e LIMIT: "arrume a fila"

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `ORDER BY` | Ordenar por | Define a ordem das linhas do resultado |
| `ASC` | Ascendente | Do menor para o maior (A→Z). É o padrão |
| `DESC` | Descendente | Do maior para o menor (Z→A) |
| `NULLS FIRST` / `NULLS LAST` | Nulos primeiro / por último | Escolhe onde os valores vazios aparecem |
| `LIMIT` | Limite | Corta o resultado em N linhas |
| `RANDOM()` | Aleatório | Gera um número sorteado (usado para embaralhar) |

> Sem `ORDER BY`, o PostgreSQL **não garante ordem** nenhuma.

---

## Ordenação simples

```sql
SELECT nome, preco FROM produtos ORDER BY preco;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `SELECT` / `FROM` | Selecionar / De | Colunas e tabela |
| `ORDER BY preco` | Ordenar por preço | Do mais barato ao mais caro (`ASC` implícito) |

```sql
SELECT nome, preco FROM produtos ORDER BY preco DESC;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `ORDER BY preco DESC` | Ordenar por preço, decrescente | Do mais caro ao mais barato |

```sql
SELECT id, cliente_id, data_pedido, data_entrega FROM pedidos ORDER BY data_entrega;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `ORDER BY data_entrega` | Ordenar por data de entrega | Em ordem crescente; no PostgreSQL os `NULL` (não entregues) vão por **último** |

```sql
SELECT id, cliente_id, data_pedido, data_entrega FROM pedidos ORDER BY 4;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `ORDER BY 4` | Ordenar pela 4ª coluna | Usa a **posição** da coluna no `SELECT` (aqui, `data_entrega`). Prático, mas frágil se o `SELECT` mudar |

---

## Controlando os nulos

```sql
SELECT id, nome, pais FROM clientes ORDER BY pais NULLS FIRST;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `ORDER BY pais` | Ordenar por país | Ordem alfabética |
| `NULLS FIRST` | Nulos primeiro | Clientes sem país cadastrado aparecem no topo (útil para achar cadastro incompleto). `NULLS LAST` faz o oposto |

---

## Desempate com vários critérios

```sql
SELECT id, cliente_id, frete, data_pedido
FROM pedidos
ORDER BY frete DESC, data_pedido ASC;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `ORDER BY a DESC, b ASC` | Ordenar por a (decrescente), depois por b (crescente) | Se dois pedidos têm o mesmo frete, o mais antigo vem antes |

---

## Ordem personalizada com `CASE`

**Entregues primeiro, pendentes depois:**

```sql
SELECT id, data_pedido, data_entrega
FROM pedidos
ORDER BY
    CASE WHEN data_entrega IS NOT NULL THEN 0   -- entregue → vem primeiro
         ELSE 1                                 -- ainda não enviado
    END,
    data_pedido;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `CASE` | Caso | Abre um "se/senão" |
| `WHEN ... THEN` | Quando ... então | Define a condição e o valor retornado |
| `ELSE` | Senão | Valor para quem não casou com nenhum `WHEN` |
| `END` | Fim | Fecha o `CASE` |
| `IS NOT NULL` | Não é nulo | Pedido que já tem data de entrega |
| `ORDER BY ... , data_pedido` | Ordenar por ..., depois por data do pedido | Dentro de cada grupo (0 e 1), ordena por data |

**Produtos descontinuados vão para o fim da lista:**

```sql
SELECT nome, descontinuado
FROM produtos
ORDER BY
    CASE
        WHEN descontinuado THEN 1   -- descontinuado → fim
        ELSE 0                      -- ativo → começo
    END,
    nome;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `CASE WHEN ... THEN ... ELSE ... END` | Caso quando ... então ... senão ... fim | Cria uma "nota de prioridade" (0 ou 1) só para ordenar |
| `ORDER BY CASE..., nome` | Ordenar pela prioridade, depois por nome | Ativos em ordem alfabética, depois descontinuados em ordem alfabética |

---

## LIMIT e sorteio

```sql
SELECT * FROM motoristas LIMIT 5;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `LIMIT 5` | Limite de 5 | Traz só 5 motoristas (sem ordem definida!) |

```sql
SELECT nome FROM motoristas ORDER BY RANDOM() LIMIT 3;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `ORDER BY RANDOM()` | Ordenar aleatoriamente | Embaralha as linhas |
| `LIMIT 3` | Limite de 3 | Fica com os 3 primeiros do embaralhamento = **sorteio** (ex.: auditoria por amostragem) |

```sql
-- Os 5 produtos mais caros
SELECT nome, preco FROM produtos ORDER BY preco DESC LIMIT 5;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `ORDER BY ... DESC LIMIT n` | Ordenar decrescente, limitar a n | Padrão clássico de **"top N"** |

---

[← SELECT e filtros](05-select-e-filtros.md) · [Índice](README.md) · [Próximo: Transformando resultados →](07-transformando-resultados.md)
