# 09 — Números e agregações

## Aritmética

| Operador | Português | Exemplo |
|----------|-----------|---------|
| `+` | soma | `preco + 5` |
| `-` | subtração | `preco - 3` |
| `*` | multiplicação | `preco * 1.5` |
| `/` | divisão | `preco / 2` |
| `%` | resto da divisão | `quantidade % 2` |

```sql
SELECT nome, preco, preco * 0.9 AS preco_promocional FROM produtos LIMIT 5;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `SELECT` | Selecionar | Escolhe colunas e cálculos |
| `preco * 0.9` | Preço vezes 0,9 | Calcula 10% de desconto na hora (sem alterar a tabela) |
| `AS` | Como | Nome da coluna calculada |
| `FROM` | De | Tabela de origem |
| `LIMIT 5` | Limite de 5 | Mostra só 5 linhas |

```sql
SELECT pedido_id, produto_id, quantidade * preco_unit AS total_item
FROM itens_pedido
LIMIT 10;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `quantidade * preco_unit` | Quantidade vezes preço unitário | Valor total de cada item do pedido |
| `LIMIT` | Limite | Corta o resultado |

```sql
SELECT
    nome,
    preco,
    preco + 5   AS somado,
    preco - 3   AS subtraido,
    preco * 1.5 AS aumentado,
    preco / 2   AS dividido
FROM produtos
LIMIT 5;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `+ - * /` | Soma, subtração, multiplicação, divisão | Cálculos linha a linha |

```sql
SELECT nome, ROUND(preco::numeric, 2) AS preco_arredondado FROM produtos LIMIT 5;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `ROUND(x, 2)` | Arredondar x a 2 casas | Evita preços como `19.9999` |
| `::numeric` | Converter para numérico | Garante o tipo que o `ROUND` aceita |

```sql
SELECT pedido_id, produto_id, preco_unit, desconto
FROM itens_pedido
WHERE desconto > 0;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `WHERE desconto > 0` | Onde desconto maior que 0 | Mostra só itens vendidos com desconto |

---

## Funções de agregação (resumem várias linhas em uma)

| Função | Português | Resultado |
|--------|-----------|-----------|
| `COUNT(*)` | Contar | Quantas linhas |
| `COUNT(coluna)` | Contar coluna | Quantas **não nulas** |
| `SUM(x)` | Somar | Soma |
| `AVG(x)` | Média (*average*) | Média |
| `MIN(x)` / `MAX(x)` | Mínimo / máximo | Menor / maior |

```sql
SELECT SUM(frete) AS frete_total FROM pedidos;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `SUM(frete)` | Soma do frete | Quanto se cobrou de frete no total |
| `AS` | Como | Nome do resultado |

```sql
SELECT
    AVG(preco) AS preco_medio,
    MIN(preco) AS menor_preco,
    MAX(preco) AS maior_preco
FROM produtos;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `AVG` | Média | Preço médio dos produtos |
| `MIN` | Mínimo | Produto mais barato |
| `MAX` | Máximo | Produto mais caro |

---

## GROUP BY — "agrupe as caixas por..."

```sql
SELECT categoria_id, COUNT(*) AS total_produtos
FROM produtos
GROUP BY categoria_id;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `COUNT(*)` | Contar linhas | Quantos produtos em cada grupo |
| `GROUP BY categoria_id` | Agrupar por categoria | Cria um grupo por categoria; o `COUNT` roda dentro de cada grupo |

```sql
-- Produtos por categoria, mostrando o NOME da categoria (versão "vírgula")
SELECT c.nome, COUNT(p.id) AS total_produtos
FROM produtos p, categorias c
WHERE p.categoria_id = c.id
GROUP BY c.nome
ORDER BY total_produtos DESC;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `FROM produtos p, categorias c` | De produtos (apelido p) e categorias (apelido c) | Usa duas tabelas; `p` e `c` são apelidos curtos |
| `WHERE p.categoria_id = c.id` | Onde as chaves coincidem | Liga cada produto à sua categoria (equivale a um `INNER JOIN`) |
| `GROUP BY c.nome` | Agrupar por nome da categoria | Um grupo por categoria |
| `ORDER BY ... DESC` | Ordenar decrescente | Categoria com mais produtos primeiro |

```sql
-- Qual o país que mais recebe pedidos?
SELECT pais_destino, COUNT(*) AS ocorrencias
FROM pedidos
GROUP BY pais_destino
ORDER BY ocorrencias DESC
LIMIT 1;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `GROUP BY pais_destino` | Agrupar por país de destino | Conta pedidos por país |
| `ORDER BY ocorrencias DESC` | Ordenar pelo total, decrescente | Maior total primeiro |
| `LIMIT 1` | Limite de 1 | Fica só com o **campeão** |

```sql
-- Qual produto mais saiu?
SELECT produto_id, COUNT(*) AS vezes_vendido
FROM itens_pedido
GROUP BY produto_id
ORDER BY vezes_vendido DESC
LIMIT 1;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `GROUP BY produto_id` | Agrupar por produto | Um grupo por produto |
| `ORDER BY ... DESC LIMIT 1` | Ordenar decrescente, limitar a 1 | Pega o mais vendido |

**Regra:** toda coluna no `SELECT` que **não** é agregada tem que estar no `GROUP BY`.

---

## WHERE × HAVING

| | Filtra | Quando | Pode usar agregação? |
|-|--------|--------|----------------------|
| `WHERE` | linhas | **antes** de agrupar | Não |
| `HAVING` | grupos | **depois** de agrupar | Sim |

```sql
-- Países com mais de 10 pedidos
SELECT pais_destino, COUNT(*) AS total
FROM pedidos
GROUP BY pais_destino
HAVING COUNT(*) > 10;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `GROUP BY` | Agrupar por | Forma um grupo por país |
| `HAVING COUNT(*) > 10` | Tendo contagem maior que 10 | Descarta os grupos pequenos **depois** de contar |

```sql
-- Frete total por país, só dos países acima de 1.000
SELECT pais_destino, SUM(frete) AS frete_total
FROM pedidos
WHERE data_entrega IS NOT NULL
GROUP BY pais_destino
HAVING SUM(frete) > 1000
ORDER BY frete_total DESC;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `WHERE data_entrega IS NOT NULL` | Onde já entregue | Filtra **linhas** antes de agrupar |
| `HAVING SUM(frete) > 1000` | Tendo soma do frete > 1000 | Filtra **grupos** depois de somar |
| `ORDER BY` | Ordenar por | Maior frete primeiro |

---

[← Texto (strings)](08-strings.md) · [Índice](README.md) · [Próximo: JOIN e UNION →](10-join-e-union.md)
