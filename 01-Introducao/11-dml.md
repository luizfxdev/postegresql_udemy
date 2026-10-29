# 11 — DML: Data Manipulation Language

**DML = Linguagem de Manipulação de Dados.** Comandos que mexem nos **dados** (as "caixas"),
sem alterar a estrutura (o "prédio" — isso é [DDL](04-ddl.md)).

| Comando | Português | Analogia logística | Operação CRUD |
|---------|-----------|--------------------|---------------|
| `INSERT` | Inserir | **Receber** mercadoria e etiquetar | Create |
| `UPDATE` | Atualizar | **Corrigir** a etiqueta / reajustar | Update |
| `DELETE` | Apagar | **Descartar** a caixa | Delete |

---

## INSERT — "receber mercadoria"

Antes de inserir, use `\d tabela` para ver colunas obrigatórias e tipos.

```sql
INSERT INTO clientes (nome, cidade, pais)
VALUES ('Tech Solutions Ltda', 'São Paulo', 'Brazil');
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `INSERT INTO` | Inserir em | Anuncia que uma linha nova vai entrar na tabela |
| `clientes (nome, cidade, pais)` | Tabela e colunas | Lista **quais colunas** receberão valor |
| `VALUES` | Valores | Os dados, **na mesma ordem** das colunas |

> O `id` ficou de fora: o `SERIAL` gera sozinho.

```sql
-- Novo produto no armazém
INSERT INTO produtos (nome, categoria_id, fornecedor_id, preco, estoque)
VALUES ('Café Gourmet', 1, 1, 25.50, 200);
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `INSERT INTO produtos (...)` | Inserir em produtos | Cadastra um produto |
| `VALUES (...)` | Valores | Texto entre aspas simples; números sem aspas |

```sql
-- várias linhas de uma vez: contratar dois motoristas
INSERT INTO motoristas (nome, contratado_em)
VALUES ('João Almeida', '2024-06-15'),
       ('Maria Souza',  '2023-01-10');
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `VALUES (...), (...)` | Valores (vários grupos) | Insere várias linhas num único comando |
| `'2024-06-15'` | Data (ano-mês-dia) | Formato padrão de data |

```sql
-- Registrar um pedido e seus itens
INSERT INTO pedidos (cliente_id, motorista_id, frete, pais_destino)
VALUES (1, 2, 45.00, 'Brazil');

INSERT INTO itens_pedido (pedido_id, produto_id, quantidade, preco_unit)
VALUES (1, 1, 10, 25.50);
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `cliente_id`, `motorista_id` | Chaves estrangeiras | Precisam apontar para linhas que **já existem** |
| `data_pedido` (omitida) | Data do pedido | Fica com o `DEFAULT CURRENT_DATE` |

### Conferir o que entrou

```sql
SELECT id, nome, preco, descontinuado
FROM produtos
ORDER BY id DESC
LIMIT 5;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `ORDER BY id DESC` | Ordenar por id decrescente | Os cadastros mais recentes ficam no topo |
| `LIMIT 5` | Limite de 5 | Mostra só os 5 últimos |

---

## UPDATE — "corrigir a etiqueta"

```sql
UPDATE produtos
SET preco = 30.00
WHERE nome = 'Café Gourmet';
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `UPDATE produtos` | Atualizar produtos | Escolhe a tabela a alterar |
| `SET preco = 30.00` | Definir preço = 30,00 | Diz **qual coluna** recebe **qual valor** |
| `WHERE` | Onde | Limita **quais linhas** serão alteradas (sem ele, altera todas!) |

```sql
-- várias colunas: novo preço e retirar de linha
UPDATE produtos
SET preco = 29.90,
    descontinuado = true
WHERE nome = 'Café Gourmet';
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `SET a = x, b = y` | Definir a = x e b = y | Altera várias colunas de uma vez, separadas por vírgula |

```sql
-- reajuste de 10% numa categoria
UPDATE produtos
SET preco = preco * 1.10
WHERE categoria_id = 1;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `preco = preco * 1.10` | Preço = preço atual vezes 1,10 | Usa o valor antigo no cálculo (+10%) |
| `WHERE categoria_id = 1` | Onde a categoria é 1 | Reajusta só essa categoria |

```sql
-- baixa de estoque após uma saída
UPDATE produtos
SET estoque = estoque - 5
WHERE id = 10;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `estoque = estoque - 5` | Estoque = estoque atual menos 5 | Abate a quantidade que saiu do armazém |

```sql
-- registrar a entrega de um pedido
UPDATE pedidos
SET data_entrega = CURRENT_DATE
WHERE id = 1 AND data_entrega IS NULL;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `CURRENT_DATE` | Data atual | Carimba a entrega com a data de hoje |
| `AND` | E | Só atualiza se o pedido **ainda** não estava entregue |
| `IS NULL` | É nulo | Testa se a data está vazia |

```sql
-- preencher nulos
UPDATE produtos
SET descricao = 'Sem informação'
WHERE descricao IS NULL;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `WHERE descricao IS NULL` | Onde a descrição é nula | Corrige só os cadastros incompletos |

---

## DELETE — "descartar a caixa"

```sql
DELETE FROM produtos
WHERE nome = 'Café Gourmet';
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `DELETE FROM produtos` | Apagar de produtos | Remove linhas dessa tabela |
| `WHERE` | Onde | Define **quais** linhas apagar (sem ele, apaga **tudo**) |

```sql
-- cancelar um pedido: apague primeiro os itens (filhos), depois o pedido (pai)
DELETE FROM itens_pedido WHERE pedido_id = 1;
DELETE FROM pedidos      WHERE id = 1;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| Ordem filho → pai | — | A `FOREIGN KEY` impede apagar o pedido enquanto houver itens apontando para ele |

---

## RETURNING — "mostre o que mexeu"

Funciona em `INSERT`, `UPDATE` e `DELETE`: devolve as linhas afetadas, sem precisar de um `SELECT` depois.

```sql
INSERT INTO produtos (nome, preco) VALUES ('Palete', 120.00) RETURNING id;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `RETURNING id` | Retornando id | Devolve o `id` que o `SERIAL` acabou de gerar |

```sql
DELETE FROM produtos
WHERE nome = 'Café Gourmet'
RETURNING *;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `RETURNING *` | Retornando tudo | Mostra as linhas que acabaram de ser apagadas (um "recibo") |

---

## ⚠️ Regra de ouro: `UPDATE` e `DELETE` **sem `WHERE` afetam a tabela inteira**

**Rotina segura (3 passos):**

1. `SELECT ... WHERE <condição>;`  → confira **quais** linhas serão afetadas
2. Troque o `SELECT` por `UPDATE`/`DELETE` mantendo o **mesmo** `WHERE`
3. Rode de novo o `SELECT` → confirme o resultado

```sql
-- 1) quem vai ser reajustado?
SELECT id, nome, preco FROM produtos WHERE categoria_id = 1;

-- 2) reajusta
UPDATE produtos SET preco = preco * 1.10 WHERE categoria_id = 1;

-- 3) confere
SELECT id, nome, preco FROM produtos WHERE categoria_id = 1;
```

Para um ensaio sem risco, use uma [transação](12-transacoes.md) e termine com `ROLLBACK`.

---

## DDL × DML — como não confundir

| | DDL | DML |
|-|-----|-----|
| Mexe em | **Estrutura** | **Dados** |
| Comandos | `CREATE` `ALTER` `DROP` `TRUNCATE` | `INSERT` `UPDATE` `DELETE` |
| Logística | Construir/reformar o armazém | Entrar/mudar/sair caixas |
| Exemplo | Criar a tabela `pedidos` | Registrar o pedido nº 1 |

---

[← JOIN e UNION](10-join-e-union.md) · [Índice](README.md) · [Próximo: Transações →](12-transacoes.md)
