# 07 — Transformando o resultado

## CASE — o "if/else" do SQL

```sql
SELECT
    id,
    frete,
    CASE
        WHEN frete < 50  THEN 'Baixo'
        WHEN frete < 100 THEN 'Médio'
        ELSE 'Alto'
    END AS faixa_frete
FROM pedidos;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `CASE` | Caso | Inicia a lógica condicional |
| `WHEN ... THEN` | Quando ... então | Se a condição for verdadeira, devolve o valor |
| `ELSE` | Senão | Valor padrão quando nenhum `WHEN` casa |
| `END` | Fim | Encerra o `CASE` |
| `AS` | Como | Dá nome à coluna calculada |
| `<` | Menor que | Compara o frete com a faixa |

O primeiro `WHEN` verdadeiro vence. Sem `ELSE`, o que sobra vira `NULL`.

**Classificar motorista por tempo de casa:**

```sql
SELECT
    id,
    nome,
    contratado_em,
    CASE
        WHEN EXTRACT(YEAR FROM AGE(CURRENT_DATE, contratado_em)) >= 5 THEN 'Sênior'
        ELSE 'Júnior'
    END AS nivel
FROM motoristas;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `EXTRACT(YEAR FROM x)` | Extrair o ano de x | Pega só o ano de uma data/intervalo |
| `AGE(a, b)` | Idade / intervalo entre a e b | Calcula a diferença entre duas datas |
| `CURRENT_DATE` | Data atual | Hoje |
| `>=` | Maior ou igual | 5 anos ou mais de casa |
| `CASE WHEN ... ELSE ... END` | Caso quando ... senão ... fim | Define Sênior ou Júnior |

---

## COALESCE — "se for NULL, use isto"

```sql
SELECT id, COALESCE(CAST(data_entrega AS TEXT), 'Em trânsito') AS situacao
FROM pedidos;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `COALESCE(a, b)` | Coalescer / "primeiro não nulo" | Devolve `a`; se `a` for `NULL`, devolve `b` |
| `CAST(x AS TEXT)` | Converter x para texto | Necessário para que data e texto possam conviver na mesma coluna |
| `AS situacao` | Como "situacao" | Nome da coluna resultante |

**Combinando `CASE` + `CAST` + `COALESCE` — esconder preço de produto barato:**

```sql
SELECT
    nome,
    preco,
    COALESCE(
        CAST(CASE WHEN preco < 20 THEN NULL ELSE preco END AS TEXT),
        'Preço oculto'
    ) AS preco_exibido
FROM produtos;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `CASE WHEN preco < 20 THEN NULL` | Caso preço < 20 então nulo | Transforma preços baixos em `NULL` |
| `CAST(... AS TEXT)` | Converter para texto | Deixa o preço compatível com a mensagem de texto |
| `COALESCE(..., 'Preço oculto')` | Primeiro não nulo | Troca o `NULL` pela mensagem |

---

## CAST e `::` — converter tipos

```sql
SELECT CAST(preco AS TEXT) FROM produtos;
SELECT preco::TEXT FROM produtos;               -- atalho do PostgreSQL
SELECT nome, ROUND(preco::numeric, 2) AS preco_arredondado FROM produtos;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `CAST(x AS tipo)` | Converter x para o tipo | Forma padrão SQL de converter |
| `x::tipo` | Converter (atalho) | Mesma coisa, mais curto (específico do PostgreSQL) |
| `ROUND(x, 2)` | Arredondar x a 2 casas | Limpa casas decimais |
| `numeric` | Numérico | Tipo decimal exato exigido pelo `ROUND` com casas |

---

## Datas

```sql
SELECT CURRENT_DATE, NOW();
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `CURRENT_DATE` | Data atual | Só o dia de hoje |
| `NOW()` | Agora | Data **e** hora atuais |

```sql
SELECT id, AGE(data_entrega, data_pedido) AS intervalo FROM pedidos;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `AGE(a, b)` | Idade / diferença | Intervalo entre as duas datas, em anos/meses/dias |

```sql
SELECT nome, EXTRACT(YEAR FROM AGE(CURRENT_DATE, contratado_em)) AS anos_de_casa
FROM motoristas;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `EXTRACT(YEAR FROM ...)` | Extrair o ano de ... | Pega só os anos completos do intervalo |

```sql
SELECT id, data_entrega - data_pedido AS dias_em_transito FROM pedidos;   -- DATE - DATE = dias
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `data_entrega - data_pedido` | Subtração de datas | Resulta em **número de dias** (tempo de trânsito) |

---

[← ORDER BY e LIMIT](06-order-by-e-limit.md) · [Índice](README.md) · [Próximo: Texto (strings) →](08-strings.md)
