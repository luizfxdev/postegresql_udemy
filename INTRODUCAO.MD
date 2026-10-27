# Introdução ao PostgreSQL — Logística Edition

> Guia rápido para **entender e memorizar**. Tema: uma operação de logística
> (clientes pedem, produtos saem do armazém, motoristas entregam).
> Dica de estudo: leia a analogia, rode o exemplo, depois feche o arquivo e tente reescrever.

---

## 1. O que é PostgreSQL?

Um **banco de dados relacional** open source. Guarda dados em **tabelas** (linhas e colunas) e
você conversa com ele usando **SQL**.

**Analogia:** o banco é o **centro de distribuição**. Cada tabela é um **corredor** (clientes, produtos,
pedidos...). Cada linha é uma **caixa** etiquetada. SQL é o **rádio** com que você pede ao operador:
"traga, mude, guarde ou descarte".

| Termo | Significado | Na logística |
|-------|-------------|--------------|
| Database | Conjunto de tabelas | O armazém inteiro |
| Table | Dados de um assunto | Corredor "pedidos" |
| Row (linha/registro) | Um item | Um pedido |
| Column (coluna) | Um atributo | Peso, destino, status |
| Primary Key (PK) | Identificador único da linha | Código de rastreio |
| Foreign Key (FK) | Aponta para a PK de outra tabela | Pedido → cliente |
| Schema | Pasta lógica dentro do banco | Setor do armazém |

---

## 2. Mapa mental — os 4 grupos de comandos

```
SQL
├── DDL  (estrutura)   CREATE · ALTER · DROP
├── DML  (dados)       INSERT · UPDATE · DELETE
├── DQL  (consulta)    SELECT
└── DCL/TCL (controle) GRANT · REVOKE · BEGIN · COMMIT · ROLLBACK
```

**Memorize:** *CRUD* = **C**reate (`INSERT`) · **R**ead (`SELECT`) · **U**pdate (`UPDATE`) · **D**elete (`DELETE`).

---

## 3. Instalando e entrando (Linux)

```bash
# entrar como usuário postgres e abrir o terminal do banco
su - postgres
psql
```

```sql
-- criar o banco
CREATE DATABASE logistica;
```

```bash
# carregar um script .sql dentro de um banco
psql -U postgres -d logistica -f logistica.sql
```

> No curso usamos o banco de exemplo **Northwind** (clientes, pedidos, produtos, fornecedores).
> Ele é "logística disfarçada": ver seção 15.

### Prompt do psql

| Prompt | Significa |
|--------|-----------|
| `#` (no shell) | Você está no terminal do Linux |
| `=#` | Você está dentro do psql como superusuário |
| `=>` | Dentro do psql como usuário comum |

### Meta-comandos do psql (começam com `\`, **sem** `;`)

| Comando | Para que serve |
|---------|----------------|
| `\l` | Listar bancos |
| `\c logistica` | Conectar a um banco |
| `\dt` | Listar tabelas |
| `\d pedidos` | Descrever a tabela (colunas, tipos, chaves) |
| `\x` | Alternar exibição expandida (1 coluna por linha — ótimo p/ tabelas largas) |
| `\q` ou `exit` | Sair |
| `\?` / `\h SELECT` | Ajuda do psql / ajuda da sintaxe SQL |

**Regra de ouro:** comando SQL termina com `;`. Meta-comando `\` não precisa.

---

## 4. Nosso cenário de logística

```sql
CREATE TABLE clientes (
    id            SERIAL PRIMARY KEY,
    nome          VARCHAR(100) NOT NULL,
    cidade        VARCHAR(60),
    pais          VARCHAR(40)
);

CREATE TABLE produtos (
    id            SERIAL PRIMARY KEY,
    nome          VARCHAR(100) NOT NULL,
    preco         NUMERIC(10,2) NOT NULL,
    estoque       INT DEFAULT 0,
    descontinuado BOOLEAN DEFAULT false
);

CREATE TABLE motoristas (
    id            SERIAL PRIMARY KEY,
    nome          VARCHAR(100) NOT NULL,
    contratado_em DATE,
    supervisor_id INT REFERENCES motoristas(id)     -- NULL = não tem chefe
);

CREATE TABLE pedidos (
    id             SERIAL PRIMARY KEY,
    cliente_id     INT NOT NULL REFERENCES clientes(id),
    motorista_id   INT REFERENCES motoristas(id),
    data_pedido    DATE DEFAULT CURRENT_DATE,
    data_entrega   DATE,                            -- NULL = ainda não entregue
    frete          NUMERIC(10,2) DEFAULT 0,
    pais_destino   VARCHAR(40)
);

CREATE TABLE itens_pedido (
    pedido_id   INT REFERENCES pedidos(id),
    produto_id  INT REFERENCES produtos(id),
    quantidade  INT NOT NULL CHECK (quantidade > 0),
    preco_unit  NUMERIC(10,2) NOT NULL,
    desconto    NUMERIC(3,2) DEFAULT 0,
    PRIMARY KEY (pedido_id, produto_id)             -- chave composta
);
```

```
clientes ──< pedidos >── motoristas
               │
          itens_pedido >── produtos
```

`──<` lê-se "um para muitos": **um** cliente tem **muitos** pedidos; **um** pedido tem **muitos** itens.

### Tipos de dados mais usados

| Tipo | Uso | Exemplo logístico |
|------|-----|-------------------|
| `INT`, `BIGINT` | Inteiros | quantidade, id |
| `SERIAL` | Inteiro auto-incrementado | id do pedido |
| `NUMERIC(10,2)` | Decimal **exato** (dinheiro!) | preço, frete |
| `FLOAT` | Decimal aproximado | peso, coordenada |
| `VARCHAR(n)` / `TEXT` | Texto | nome, endereço |
| `BOOLEAN` | true/false | descontinuado, entregue |
| `DATE` / `TIMESTAMP` | Data / data+hora | data de entrega |

**Dinheiro = `NUMERIC`, nunca `FLOAT`** (float arredonda errado).

### Constraints (as regras da casa)

| Constraint | Garante |
|-----------|---------|
| `PRIMARY KEY` | Único e não nulo |
| `FOREIGN KEY` / `REFERENCES` | Só aponta para algo que existe |
| `NOT NULL` | Obrigatório |
| `UNIQUE` | Sem repetição |
| `DEFAULT` | Valor padrão |
| `CHECK` | Regra própria (`quantidade > 0`) |

---

## 5. SELECT — "me traga isso"

**Estrutura (decore a ordem):**

```
SELECT   o quê
FROM     de onde
WHERE    filtre linhas
GROUP BY agrupe
HAVING   filtre grupos
ORDER BY ordene
LIMIT    corte
```

> Mnemônico: **S**empre **F**aça **W**hat **G**roup **H**ave **O**rder **L**imit
> (ou: "**S**eu **F**retado **W**ill **G**anhar **H**oje **O**s **L**ucros").

```sql
SELECT * FROM clientes;                              -- tudo (evite em produção)
SELECT nome, cidade FROM clientes;                   -- só colunas necessárias
SELECT * FROM clientes WHERE pais = 'Brazil';        -- filtrando
SELECT * FROM clientes LIMIT 5;                      -- só 5 linhas
```

### Alias com `AS` (apelido)

```sql
SELECT nome AS cliente, cidade AS destino FROM clientes;
```

### Operadores no WHERE

| Operador | Significado | Exemplo |
|----------|-------------|---------|
| `=` `<>` (ou `!=`) | Igual / diferente | `pais = 'Brazil'` |
| `>` `<` `>=` `<=` | Comparação | `estoque < 10` |
| `AND` | Todas verdadeiras | `estoque < 10 AND descontinuado = false` |
| `OR` | Pelo menos uma | `pais = 'Brazil' OR pais = 'Mexico'` |
| `NOT` | Inverte | `NOT descontinuado` |
| `IN (...)` | Está na lista | `pais IN ('Brazil','Mexico')` |
| `BETWEEN a AND b` | Intervalo (inclusive) | `frete BETWEEN 10 AND 50` |
| `LIKE` / `ILIKE` | Padrão de texto (`ILIKE` ignora maiúscula) | `nome ILIKE '%express%'` |
| `IS NULL` / `IS NOT NULL` | Vazio / preenchido | `data_entrega IS NULL` |

```sql
-- Produtos com estoque crítico OU descontinuados
SELECT id, nome, estoque, descontinuado
FROM produtos
WHERE estoque < 10 OR descontinuado = true;

-- Use parênteses para misturar AND e OR!
SELECT id, nome
FROM produtos
WHERE categoria_id = 1
  AND (estoque < 10 OR descontinuado = true);
```

### NULL — a armadilha clássica

`NULL` = "**não sei / não existe**", não é zero nem texto vazio.

```sql
WHERE data_entrega = NULL       -- ERRADO, nunca é verdadeiro
WHERE data_entrega IS NULL      -- CERTO  → pedidos ainda em trânsito
```

**Analogia:** pacote sem data de entrega não é "entregue em zero". É "ainda a caminho".

---

## 6. Transformando o resultado

### CASE — o "if/else" do SQL

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

O primeiro `WHEN` verdadeiro vence. Sem `ELSE`, o que sobra vira `NULL`.

### COALESCE — "se for NULL, use isto"

```sql
SELECT id, COALESCE(CAST(data_entrega AS TEXT), 'Em trânsito') AS situacao
FROM pedidos;
```

### CAST / `::` — converter tipos

```sql
SELECT CAST(preco AS TEXT);
SELECT preco::TEXT;              -- atalho do PostgreSQL
SELECT ROUND(preco::numeric, 2);
```

### Datas

```sql
SELECT CURRENT_DATE, NOW();
SELECT AGE(data_entrega, data_pedido) FROM pedidos;                       -- intervalo
SELECT EXTRACT(YEAR FROM AGE(CURRENT_DATE, contratado_em)) AS anos_casa
FROM motoristas;
SELECT data_entrega - data_pedido AS dias_transito FROM pedidos;          -- DATE - DATE = dias
```

---

## 7. ORDER BY e LIMIT — "arrume a fila"

```sql
SELECT nome, preco FROM produtos ORDER BY preco;                 -- crescente (ASC é padrão)
SELECT nome, preco FROM produtos ORDER BY preco DESC;            -- decrescente
SELECT * FROM pedidos ORDER BY 4;                                -- pela 4ª coluna do SELECT
SELECT * FROM pedidos ORDER BY frete DESC, data_pedido ASC;      -- desempate
SELECT * FROM clientes ORDER BY pais NULLS FIRST;                -- NULL no topo (NULLS LAST = no fim)
SELECT nome FROM motoristas ORDER BY RANDOM() LIMIT 3;           -- sorteio de 3
```

**Ordem personalizada com CASE** (entregues primeiro, pendentes depois):

```sql
SELECT id, data_pedido, data_entrega
FROM pedidos
ORDER BY
    CASE WHEN data_entrega IS NOT NULL THEN 0 ELSE 1 END,
    data_pedido;
```

> Sem `ORDER BY`, o PostgreSQL **não garante ordem** nenhuma.

---

## 8. Texto (strings)

| Função | O que faz | Exemplo |
|--------|-----------|---------|
| `LIKE 'C%'` | Começa com C | `nome LIKE 'C%'` |
| `ILIKE '%food%'` | Contém "food" (sem case) | `nome ILIKE '%food%'` |
| `%` / `_` | Vários caracteres / exatamente um | `'A_c%'` |
| `LOWER` / `UPPER` | Minúscula / maiúscula | `UPPER(nome)` |
| `INITCAP` | Primeira letra maiúscula | `INITCAP('joão silva')` |
| `LENGTH` | Tamanho | `LENGTH(nome) > 20` |
| `SUBSTRING(x FROM 1 FOR 3)` | Pedaço do texto | prefixo de 3 letras |
| `REPLACE(x,'a','b')` | Troca texto | `REPLACE(nome,'Ltd.','Limited')` |
| `POSITION('x' IN y)` | Posição (0 = não achou) | `POSITION('Log' IN nome)` |
| `CONCAT(a,' ',b)` ou `a \|\| ' ' \|\| b` | Junta textos | nome completo |

```sql
SELECT CONCAT(nome, ' (', cidade, ')') AS cliente_local FROM clientes;
```

> `CONCAT` ignora `NULL`; o operador `||` devolve `NULL` se qualquer parte for `NULL`.

---

## 9. Números e agregações

### Aritmética

`+  -  *  /  %` (resto)

```sql
SELECT nome, preco, preco * 0.9 AS preco_promocional FROM produtos;
SELECT pedido_id, produto_id, quantidade * preco_unit AS total_item FROM itens_pedido;
```

### Funções de agregação (resumem várias linhas em uma)

| Função | Resultado |
|--------|-----------|
| `COUNT(*)` | Quantas linhas |
| `COUNT(coluna)` | Quantas **não nulas** |
| `SUM(x)` | Soma |
| `AVG(x)` | Média |
| `MIN(x)` / `MAX(x)` | Menor / maior |

```sql
SELECT SUM(frete) AS frete_total FROM pedidos;
SELECT AVG(preco) AS media, MIN(preco) AS menor, MAX(preco) AS maior FROM produtos;
```

### GROUP BY — "agrupe as caixas por..."

```sql
-- Pedidos por país de destino, do maior para o menor
SELECT pais_destino, COUNT(*) AS total_pedidos
FROM pedidos
GROUP BY pais_destino
ORDER BY total_pedidos DESC;

-- Qual o país que mais recebe? → LIMIT 1
... ORDER BY total_pedidos DESC LIMIT 1;
```

**Regra:** toda coluna no `SELECT` que **não** é agregada tem que estar no `GROUP BY`.

### WHERE × HAVING

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

---

## 10. JOIN — ligando tabelas

**Analogia:** o pedido só guarda o `cliente_id` (o número da etiqueta). O `JOIN` vai ao corredor
"clientes" buscar o nome correspondente.

```sql
SELECT p.id, c.nome AS cliente, p.frete
FROM pedidos p
JOIN clientes c ON c.id = p.cliente_id;
```

| Tipo | Traz | Uso típico |
|------|------|-----------|
| `INNER JOIN` (ou só `JOIN`) | Só o que casa nos dois lados | Pedidos com cliente |
| `LEFT JOIN` | Tudo da esquerda + o que casar | **Todos** os clientes, mesmo sem pedido |
| `RIGHT JOIN` | Tudo da direita + o que casar | Inverso do LEFT |
| `FULL JOIN` | Tudo dos dois lados | Auditoria / conciliação |

```sql
-- Clientes que NUNCA fizeram pedido
SELECT c.nome
FROM clientes c
LEFT JOIN pedidos p ON p.cliente_id = c.id
WHERE p.id IS NULL;

-- Produtos por categoria, versão "vírgula" (equivale ao INNER JOIN)
SELECT c.nome, COUNT(p.id)
FROM produtos p, categorias c
WHERE p.categoria_id = c.id
GROUP BY c.nome;
```

> Prefira `JOIN ... ON`: é mais legível e evita o produto cartesiano acidental.

---

## 11. UNION — empilhando resultados

Junta **linhas** de dois `SELECT`s (JOIN junta **colunas**). Precisam ter o mesmo número e tipo de colunas.

```sql
SELECT nome, 'Cliente' AS tipo FROM clientes
UNION
SELECT nome, 'Fornecedor' AS tipo FROM fornecedores
ORDER BY nome;
```

| | Comportamento |
|-|---------------|
| `UNION` | Junta e **remove duplicados** |
| `UNION ALL` | Junta e **mantém** duplicados (mais rápido) |

`ORDER BY` só vale **no final** de todo o `UNION`.

---

## 12. Mexendo nos dados — INSERT · UPDATE · DELETE

### INSERT — "receber mercadoria"

```sql
INSERT INTO clientes (nome, cidade, pais)
VALUES ('Tech Solutions Ltda', 'São Paulo', 'Brazil');

-- várias linhas de uma vez
INSERT INTO motoristas (nome, contratado_em)
VALUES ('João Almeida', '2024-06-15'),
       ('Maria Souza',  '2023-01-10');
```

Antes de inserir, use `\d tabela` para ver colunas obrigatórias e tipos.

### UPDATE — "corrigir a etiqueta"

```sql
UPDATE produtos
SET preco = 30.00
WHERE nome = 'Café Gourmet';

-- várias colunas
UPDATE produtos
SET preco = 29.90, descontinuado = true
WHERE nome = 'Café Gourmet';

-- reajuste de 10% numa categoria
UPDATE produtos
SET preco = preco * 1.10
WHERE categoria_id = 1;

-- preencher nulos
UPDATE produtos
SET descricao = 'Sem informação'
WHERE descricao IS NULL;
```

### DELETE — "descartar a caixa"

```sql
DELETE FROM produtos WHERE nome = 'Café Gourmet';

-- mostrando o que foi apagado
DELETE FROM produtos WHERE nome = 'Café Gourmet' RETURNING *;
```

### RETURNING

Funciona em `INSERT`, `UPDATE` e `DELETE`: devolve as linhas afetadas, sem precisar de um `SELECT` depois.

```sql
INSERT INTO produtos (nome, preco) VALUES ('Palete', 120.00) RETURNING id;
```

### ⚠️ Regra de ouro: `UPDATE` e `DELETE` **sem `WHERE` afetam a tabela inteira**

**Rotina segura (3 passos):**

1. `SELECT ... WHERE <condição>;`  → confira **quais** linhas serão afetadas
2. Troque o `SELECT` por `UPDATE`/`DELETE` mantendo o **mesmo** `WHERE`
3. Rode de novo o `SELECT` → confirme o resultado

---

## 13. Transações — "tudo ou nada"

**Analogia:** a carga só sai do armazém se **todas** as etapas funcionarem (baixar estoque + criar
pedido + gerar entrega). Se uma falhar, desfaz tudo.

```sql
BEGIN;
    UPDATE produtos SET estoque = estoque - 5 WHERE id = 10;
    INSERT INTO itens_pedido (pedido_id, produto_id, quantidade, preco_unit)
    VALUES (1, 10, 5, 25.50);
COMMIT;      -- confirma tudo
-- ou
ROLLBACK;    -- desfaz tudo desde o BEGIN
```

Propriedades **ACID**: **A**tomicidade (tudo ou nada) · **C**onsistência (regras sempre válidas) ·
**I**solamento (transações não se atropelam) · **D**urabilidade (confirmou, está salvo).

Dica: dentro de `BEGIN`, teste o `DELETE`/`UPDATE`, confira com `SELECT` e só então `COMMIT`.

---

## 14. Extras que valem conhecer

### Subconsulta (SELECT dentro de SELECT)

```sql
-- Produtos mais caros que a média
SELECT nome, preco
FROM produtos
WHERE preco > (SELECT AVG(preco) FROM produtos);
```

### Índice — o "mapa de corredores"

Acelera buscas em colunas muito filtradas, ao custo de mais espaço e escrita mais lenta.

```sql
CREATE INDEX idx_pedidos_cliente ON pedidos (cliente_id);
```

### EXPLAIN — "como o banco vai buscar?"

```sql
EXPLAIN ANALYZE SELECT * FROM pedidos WHERE cliente_id = 7;
```

Se aparecer `Seq Scan` numa tabela enorme, talvez falte um índice.

### View — consulta salva com nome

```sql
CREATE VIEW pedidos_pendentes AS
SELECT p.id, c.nome AS cliente, p.data_pedido
FROM pedidos p
JOIN clientes c ON c.id = p.cliente_id
WHERE p.data_entrega IS NULL;

SELECT * FROM pedidos_pendentes;
```

### ALTER / DROP

```sql
ALTER TABLE pedidos ADD COLUMN observacao TEXT;
ALTER TABLE pedidos DROP COLUMN observacao;
DROP TABLE nome_da_tabela;       -- apaga a tabela inteira (sem volta!)
```

### Backup rápido

```bash
pg_dump -U postgres logistica > backup.sql        # exporta
psql -U postgres -d logistica -f backup.sql       # restaura
```

---

## 15. Ponte com o curso (banco Northwind)

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

**Carregar o Northwind:**

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

### Mapa das aulas

| Aula | Tema | Seção deste guia |
|------|------|------------------|
| 2 | Popular o banco | 3 e 15 |
| 3 | SELECT básico, `\x`, WHERE | 5 |
| 4 | Alias, CONCAT, CASE | 5 e 6 |
| 5 | LIMIT, RANDOM, NULL, COALESCE | 5, 6 e 7 |
| 6 | ORDER BY | 7 |
| 7 | UNION | 11 |
| 9 | Strings | 8 |
| 10–11 | Números, agregações, GROUP BY | 9 |
| 12 | INSERT | 12 |
| 13 | UPDATE | 12 |
| 14 | DELETE | 12 |

---

## 16. Erros comuns (e como evitar)

| Erro | Causa | Solução |
|------|-------|---------|
| Esquecer o `;` | Prompt vira `-#` e fica esperando | Digite `;` e Enter |
| `WHERE x = NULL` | NULL não se compara com `=` | `IS NULL` |
| `UPDATE`/`DELETE` sem `WHERE` | Afeta tudo | Rotina segura da seção 12 |
| Coluna fora do `GROUP BY` | Mistura agregada e não agregada | Inclua no `GROUP BY` |
| Aspas duplas no valor | `"x"` é nome de coluna | Texto vai em aspas **simples**: `'x'` |
| `AND`/`OR` sem parênteses | Precedência inesperada (`AND` vem antes de `OR`) | Use `( )` |
| `FLOAT` para dinheiro | Arredondamento impreciso | `NUMERIC(10,2)` |
| Confiar na ordem sem `ORDER BY` | Ordem não é garantida | Sempre declare |
| `SELECT *` em tudo | Lento e frágil | Liste as colunas |
| Concatenar texto em SQL vindo de usuário | **SQL Injection** | Use parâmetros (`PreparedStatement`) |

---

## 17. Cola relâmpago (cheat sheet)

```sql
-- CONSULTAR
SELECT col1, col2 FROM tabela WHERE cond GROUP BY col HAVING cond ORDER BY col DESC LIMIT 10;

-- JUNTAR
SELECT ... FROM a JOIN b ON a.id = b.a_id;
SELECT ... UNION [ALL] SELECT ...;

-- ALTERAR
INSERT INTO t (c1, c2) VALUES (v1, v2) RETURNING *;
UPDATE t SET c1 = v1 WHERE cond RETURNING *;
DELETE FROM t WHERE cond RETURNING *;

-- TRANSAÇÃO
BEGIN; ... COMMIT;   -- ou ROLLBACK;
```

```
psql:  \l  \c  \dt  \d tabela  \x  \q  \?
```

### Frases para memorizar

1. **`WHERE` filtra linhas, `HAVING` filtra grupos.**
2. **`NULL` só se testa com `IS`.**
3. **Sem `WHERE`, `UPDATE` e `DELETE` mexem em tudo.**
4. **`UNION` empilha linhas, `JOIN` junta colunas.**
5. **Texto entre aspas simples, nomes entre aspas duplas.**
6. **Antes de alterar: `SELECT`. Depois de alterar: `SELECT`.**
7. **Dinheiro é `NUMERIC`.**

---

## 18. Exercícios de fixação (logística)

1. Liste os 5 produtos mais caros.
2. Liste clientes do Brasil cujo nome contenha "log" (sem diferenciar maiúsculas).
3. Mostre os pedidos ainda não entregues, do mais antigo ao mais recente.
4. Classifique cada pedido como `Rápido` (até 3 dias), `Normal` (4 a 7) ou `Lento` (mais de 7) usando `CASE`.
5. Descubra o total de frete por país de destino, só dos países com frete acima de 1.000.
6. Liste os clientes que nunca fizeram pedido (`LEFT JOIN`).
7. Reajuste em 5% o preço dos produtos com estoque acima de 100 (faça o `SELECT` antes!).
8. Dentro de uma transação, apague um pedido de teste usando `RETURNING *` e depois faça `ROLLBACK`.

> Se resolveu os 8 sem consultar, você domina o básico de PostgreSQL.

---

## Referências

- Documentação oficial: <https://www.postgresql.org/docs/current/>
- ORDER BY: <https://www.postgresql.org/docs/current/queries-order.html>
- CAST: <https://www.postgresql.org/docs/current/sql-createcast.html>
- Funções de texto: <https://www.postgresql.org/docs/current/functions-string.html>
- Banco de exemplo Northwind: <https://github.com/pthom/northwind_psql>
