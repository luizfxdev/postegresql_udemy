# 05 — SELECT e filtros: "me traga isso"

## Estrutura (decore a ordem)

```
SELECT   o quê
FROM     de onde
WHERE    filtre linhas
GROUP BY agrupe
HAVING   filtre grupos
ORDER BY ordene
LIMIT    corte
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `SELECT` | Selecionar | Escolhe **quais colunas** mostrar |
| `FROM` | De | Diz **de qual tabela** vêm os dados |
| `WHERE` | Onde | Filtra **linhas** antes de agrupar |
| `GROUP BY` | Agrupar por | Junta linhas iguais num só grupo |
| `HAVING` | Tendo | Filtra **grupos** depois de agrupar |
| `ORDER BY` | Ordenar por | Define a ordem do resultado |
| `LIMIT` | Limite | Corta a quantidade de linhas devolvidas |

> Mnemônico: **S**empre **F**aça **W**hat **G**roup **H**ave **O**rder **L**imit
> (ou: "**S**eu **F**retado **W**ill **G**anhar **H**oje **O**s **L**ucros").

---

## Consultas básicas

```sql
SELECT * FROM clientes;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `SELECT` | Selecionar | Pede dados |
| `*` | Todas as colunas | Traz tudo (evite em produção) |
| `FROM clientes` | De clientes | Lê da tabela `clientes` |

```sql
SELECT nome, cidade FROM clientes;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `SELECT nome, cidade` | Selecionar nome, cidade | Traz **só** as colunas necessárias (mais rápido e claro) |
| `FROM` | De | Tabela de origem |

```sql
SELECT * FROM clientes WHERE pais = 'Brazil';
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `WHERE` | Onde | Mantém só as linhas em que a condição é verdadeira |
| `=` | Igual a | Compara valores (aqui, país igual a Brasil) |

```sql
SELECT * FROM clientes LIMIT 5;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `LIMIT 5` | Limite de 5 | Devolve no máximo 5 linhas (bom para "espiar" a tabela) |

---

## Alias com `AS` (apelido)

```sql
SELECT nome AS cliente, cidade AS destino FROM clientes;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `AS` | Como | Dá um **apelido** à coluna no resultado (não muda a tabela) |
| `FROM` | De | Tabela de origem |

---

## Operadores no WHERE

### Comparação

| Operador | Português | Significado | Exemplo |
|----------|-----------|-------------|---------|
| `=` | igual | Valores iguais | `pais = 'Brazil'` |
| `<>` ou `!=` | diferente | Valores diferentes | `pais <> 'Brazil'` |
| `>` | maior que | Estritamente maior | `frete > 100` |
| `<` | menor que | Estritamente menor | `estoque < 10` |
| `>=` | maior ou igual | Maior ou igual | `preco >= 100` |
| `<=` | menor ou igual | Menor ou igual | `preco <= 20` |

### Operadores lógicos e especiais

| Operador | Português | Para que serve | Exemplo logístico |
|----------|-----------|----------------|-------------------|
| `AND` | **E** | Exige que **todas** as condições sejam verdadeiras | `estoque < 10 AND descontinuado = false` → estoque baixo **e** ainda ativo |
| `OR` | **OU** | Basta **uma** condição ser verdadeira | `pais = 'Brazil' OR pais = 'Mexico'` → entregas para um **ou** outro país |
| `NOT` | **NÃO** | **Inverte** o resultado da condição | `NOT descontinuado` → produtos que **não** foram descontinuados |
| `IN (...)` | **EM** (está na lista) | Substitui vários `OR` na mesma coluna | `pais IN ('Brazil','Mexico')` |
| `BETWEEN a AND b` | **ENTRE** | Valor dentro de um intervalo (inclusive nas pontas) | `frete BETWEEN 10 AND 50` |
| `LIKE` | **COMO / PARECIDO COM** | Busca por padrão de texto (`%` = qualquer coisa) | `nome LIKE 'C%'` |
| `ILIKE` | **COMO** (ignora maiúscula) | Igual ao `LIKE`, sem diferenciar maiúscula/minúscula | `nome ILIKE '%express%'` |
| `IS NULL` | **É NULO** | Testa se o campo está vazio | `data_entrega IS NULL` → ainda em trânsito |
| `IS NOT NULL` | **NÃO É NULO** | Testa se o campo está preenchido | `data_entrega IS NOT NULL` → já entregue |

**Tabela-verdade rápida** (V = verdadeiro, F = falso):

| A | B | `A AND B` | `A OR B` | `NOT A` |
|---|---|-----------|----------|---------|
| V | V | V | V | F |
| V | F | F | V | F |
| F | V | F | V | V |
| F | F | F | F | V |

---

## Combinando condições

```sql
-- Produtos com estoque crítico OU descontinuados
SELECT id, nome, estoque, descontinuado
FROM produtos
WHERE estoque < 10 OR descontinuado = true;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `SELECT` | Selecionar | Escolhe as colunas |
| `FROM` | De | Tabela `produtos` |
| `WHERE` | Onde | Filtra as linhas |
| `OR` | Ou | Basta estoque baixo **ou** produto descontinuado para a linha aparecer |
| `<` | Menor que | Estoque abaixo de 10 |

```sql
-- Use parênteses para misturar AND e OR!
SELECT id, nome
FROM produtos
WHERE categoria_id = 1
  AND (estoque < 10 OR descontinuado = true);
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `WHERE` | Onde | Filtra as linhas |
| `AND` | E | A categoria **precisa** ser 1 **e** a condição entre parênteses também |
| `OR` | Ou | Dentro dos parênteses: estoque baixo **ou** descontinuado |
| `( )` | Parênteses | Forçam a ordem de avaliação (sem eles, `AND` é avaliado antes de `OR`) |

---

## Filtros com lista, intervalo e `NOT`

```sql
SELECT nome, pais FROM clientes WHERE pais IN ('Brazil', 'Mexico');
SELECT id, frete FROM pedidos WHERE frete BETWEEN 10 AND 50;
SELECT nome FROM produtos WHERE NOT descontinuado;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `IN` | Em | Aceita qualquer valor da lista (Brasil **ou** México) |
| `BETWEEN ... AND` | Entre ... e | Frete de 10 **até** 50, incluindo 10 e 50 |
| `NOT` | Não | Inverte: traz os produtos que **não** estão descontinuados |

---

## NULL — a armadilha clássica

`NULL` = "**não sei / não existe**", não é zero nem texto vazio.

```sql
WHERE data_entrega = NULL       -- ERRADO, nunca é verdadeiro
WHERE data_entrega IS NULL      -- CERTO  → pedidos ainda em trânsito
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `NULL` | Nulo | Representa "valor desconhecido/ausente" |
| `=` | Igual | **Não funciona** com `NULL` (resultado é sempre "desconhecido") |
| `IS NULL` | É nulo | A forma correta de testar ausência de valor |

**Analogia:** pacote sem data de entrega não é "entregue em zero". É "ainda a caminho".

```sql
SELECT * FROM motoristas WHERE supervisor_id IS NULL;       -- motoristas sem chefe
SELECT * FROM motoristas WHERE supervisor_id IS NOT NULL;   -- motoristas com chefe
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `IS NULL` | É nulo | Acha quem **não tem** supervisor |
| `IS NOT NULL` | Não é nulo | Acha quem **tem** supervisor |

---

[← DDL](04-ddl.md) · [Índice](README.md) · [Próximo: ORDER BY e LIMIT →](06-order-by-e-limit.md)
