# 08 — Texto (strings)

## Resumo das funções

| Função | Português | O que faz | Exemplo |
|--------|-----------|-----------|---------|
| `LIKE 'C%'` | Como | Começa com C | `nome LIKE 'C%'` |
| `ILIKE '%food%'` | Como (sem case) | Contém "food", ignorando maiúscula | `nome ILIKE '%food%'` |
| `%` / `_` | Curinga | Vários caracteres / exatamente um | `'A_c%'` |
| `LOWER` / `UPPER` | Minúscula / maiúscula | Muda a caixa do texto | `UPPER(nome)` |
| `INITCAP` | Capitalizar | Primeira letra de cada palavra maiúscula | `INITCAP('joão silva')` |
| `LENGTH` | Tamanho | Conta caracteres | `LENGTH(nome) > 20` |
| `SUBSTRING(x FROM 1 FOR 3)` | Subtexto | Pedaço do texto | prefixo de 3 letras |
| `REPLACE(x,'a','b')` | Substituir | Troca texto | `REPLACE(nome,'Ltd.','Limited')` |
| `POSITION('x' IN y)` | Posição | Onde `x` aparece em `y` (0 = não achou) | `POSITION('Log' IN nome)` |
| `CONCAT(a,' ',b)` ou `a \|\| ' ' \|\| b` | Concatenar | Junta textos | nome completo |

---

## Buscar por padrão — `LIKE` e `ILIKE`

```sql
SELECT nome
FROM produtos
WHERE nome LIKE 'C%';
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `WHERE` | Onde | Filtra as linhas |
| `LIKE` | Como / parecido com | Compara com um **padrão** (diferencia maiúscula de minúscula) |
| `'C%'` | Começa com C | O `%` representa "qualquer coisa depois" |

```sql
SELECT nome
FROM clientes
WHERE nome ILIKE '%express%';
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `ILIKE` | Como (insensível a maiúscula) | Acha "Express", "EXPRESS" e "express" |
| `'%express%'` | Contém "express" | `%` dos dois lados = em qualquer posição |

---

## Maiúscula e minúscula

```sql
SELECT
    nome,
    LOWER(nome) AS nome_minusculo,
    UPPER(nome) AS nome_maiusculo
FROM clientes;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `LOWER(x)` | Minúsculo | Converte tudo para minúsculas |
| `UPPER(x)` | Maiúsculo | Converte tudo para maiúsculas |
| `AS` | Como | Nomeia cada coluna calculada |

---

## Tamanho

```sql
SELECT
    nome,
    LENGTH(nome) AS tamanho
FROM produtos
WHERE LENGTH(nome) > 20;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `LENGTH(x)` | Comprimento | Conta quantos caracteres tem o texto |
| `WHERE LENGTH(nome) > 20` | Onde o tamanho > 20 | Acha nomes longos (ex.: que podem estourar a etiqueta) |

---

## Pedaço do texto

```sql
SELECT
    nome,
    SUBSTRING(nome FROM 1 FOR 3) AS prefixo
FROM clientes;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `SUBSTRING(x FROM 1 FOR 3)` | Subtexto de x a partir de 1, por 3 caracteres | Extrai os 3 primeiros caracteres (ex.: gerar sigla) |

---

## Trocar texto

```sql
SELECT
    nome,
    REPLACE(nome, 'Ltd.', 'Limited') AS nome_ajustado
FROM fornecedores
WHERE nome LIKE '%Ltd%';
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `REPLACE(x, 'a', 'b')` | Substituir 'a' por 'b' em x | Padroniza grafias |
| `LIKE '%Ltd%'` | Como "contém Ltd" | Filtra só fornecedores que têm esse trecho |
| `WHERE` | Onde | Restringe às linhas afetadas |

---

## Posição

```sql
SELECT
    nome,
    POSITION('Log' IN nome) AS posicao
FROM clientes
WHERE nome ILIKE '%Log%';
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `POSITION('Log' IN nome)` | Posição de "Log" em nome | Devolve em que caractere começa (0 = não existe) |
| `ILIKE` | Como (sem case) | Filtra quem contém "log" em qualquer caixa |

---

## Juntar e formatar texto

```sql
SELECT CONCAT(nome, ' (', cidade, ')') AS cliente_local FROM clientes;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `CONCAT(a, b, ...)` | Concatenar | Junta vários textos em um (trata `NULL` como vazio) |
| `AS` | Como | Nome da coluna resultante |

```sql
SELECT
    LOWER(nome) AS original,
    INITCAP(LOWER(nome)) AS formatado
FROM motoristas;

SELECT nome || ' - ' || cidade AS cliente_local FROM clientes;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `INITCAP(x)` | Capitalizar | Deixa cada palavra com inicial maiúscula ("joão silva" → "João Silva") |
| `LOWER(x)` | Minúsculo | Normaliza antes de capitalizar |
| `\|\|` | Concatenação | Operador que junta textos |

> `CONCAT` ignora `NULL`; o operador `||` devolve `NULL` se qualquer parte for `NULL`.

---

[← Transformando resultados](07-transformando-resultados.md) · [Índice](README.md) · [Próximo: Números e agregações →](09-numeros-e-agregacoes.md)
