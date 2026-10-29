# 04 — DDL: Data Definition Language

**DDL = Linguagem de Definição de Dados.** Comandos que mexem na **estrutura** (o "prédio"),
não nos dados (as "caixas"). Na logística: construir corredores, trocar prateleiras, demolir setores.

| Comando | Português | Analogia logística |
|---------|-----------|--------------------|
| `CREATE` | Criar | Construir um corredor novo |
| `ALTER` | Alterar | Reformar o corredor (nova prateleira) |
| `DROP` | Descartar / derrubar | Demolir o corredor **com tudo dentro** |
| `TRUNCATE` | Esvaziar | Tirar todas as caixas, mantendo o corredor |

---

## CREATE TABLE — "construir o corredor"

Ordem importa: crie primeiro as tabelas **referenciadas** (`categorias`, `fornecedores`, `clientes`,
`motoristas`) e só depois as que apontam para elas.

```sql
CREATE TABLE categorias (
    id    SERIAL PRIMARY KEY,
    nome  VARCHAR(60) NOT NULL UNIQUE
);

CREATE TABLE fornecedores (
    id      SERIAL PRIMARY KEY,
    nome    VARCHAR(100) NOT NULL,
    cidade  VARCHAR(60),
    pais    VARCHAR(40)
);

CREATE TABLE clientes (
    id            SERIAL PRIMARY KEY,
    nome          VARCHAR(100) NOT NULL,
    cidade        VARCHAR(60),
    pais          VARCHAR(40)
);
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `CREATE TABLE` | Criar tabela | Define uma nova tabela (um novo corredor) |
| `SERIAL` | Serial (sequencial) | Número que cresce sozinho a cada linha nova |
| `PRIMARY KEY` | Chave primária | Identificador único e obrigatório da linha |
| `VARCHAR(n)` | Texto variável (até n) | Guarda texto com tamanho máximo |
| `NOT NULL` | Não nulo | Proíbe deixar o campo vazio |
| `UNIQUE` | Único | Proíbe valores repetidos na coluna |

```sql
CREATE TABLE produtos (
    id             SERIAL PRIMARY KEY,
    nome           VARCHAR(100) NOT NULL,
    descricao      TEXT,
    categoria_id   INT REFERENCES categorias(id),
    fornecedor_id  INT REFERENCES fornecedores(id),
    preco          NUMERIC(10,2) NOT NULL,
    estoque        INT DEFAULT 0,
    descontinuado  BOOLEAN DEFAULT false
);

CREATE TABLE motoristas (
    id             SERIAL PRIMARY KEY,
    nome           VARCHAR(100) NOT NULL,
    contratado_em  DATE,
    supervisor_id  INT REFERENCES motoristas(id)     -- NULL = não tem chefe
);
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `REFERENCES tabela(coluna)` | Referencia | Cria a chave estrangeira: só aceita valor que exista na outra tabela |
| `NUMERIC(10,2)` | Numérico (10 dígitos, 2 decimais) | Decimal exato, ideal para dinheiro |
| `INT` | Inteiro | Números sem casa decimal |
| `TEXT` | Texto livre | Texto sem limite de tamanho |
| `DEFAULT` | Padrão | Valor usado quando você não informa nada |
| `BOOLEAN` | Booleano | Verdadeiro (`true`) ou falso (`false`) |
| `DATE` | Data | Dia/mês/ano |

```sql
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

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `CURRENT_DATE` | Data atual | Devolve o dia de hoje (usado como padrão) |
| `CHECK (condição)` | Verificar | Rejeita a linha se a regra for falsa (ex.: `quantidade > 0`) |
| `PRIMARY KEY (a, b)` | Chave primária composta | A **combinação** de duas colunas é única (um produto só aparece uma vez por pedido) |

---

## ALTER TABLE — "reformar o corredor"

```sql
ALTER TABLE pedidos ADD COLUMN observacao TEXT;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `ALTER TABLE` | Alterar tabela | Muda a estrutura de uma tabela existente |
| `ADD COLUMN` | Adicionar coluna | Cria uma coluna nova (nas linhas antigas ela vem `NULL`) |

```sql
ALTER TABLE pedidos DROP COLUMN observacao;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `DROP COLUMN` | Descartar coluna | Remove a coluna **e os dados dela** |

Outras alterações comuns:

```sql
ALTER TABLE produtos RENAME COLUMN estoque TO qtd_estoque;          -- renomear coluna
ALTER TABLE produtos ALTER COLUMN preco SET NOT NULL;               -- tornar obrigatória
ALTER TABLE pedidos ADD CONSTRAINT chk_frete CHECK (frete >= 0);    -- nova regra
ALTER TABLE pedidos RENAME TO ordens_entrega;                       -- renomear tabela
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `RENAME COLUMN ... TO` | Renomear coluna para | Troca o nome de uma coluna |
| `ALTER COLUMN ... SET NOT NULL` | Alterar coluna: definir não nulo | Passa a exigir valor na coluna |
| `ADD CONSTRAINT` | Adicionar restrição | Cria uma regra nomeada (fácil de remover depois) |
| `RENAME TO` | Renomear para | Troca o nome da tabela |

---

## DROP e TRUNCATE — "demolir" × "esvaziar"

```sql
DROP TABLE nome_da_tabela;                 -- apaga a tabela inteira (sem volta!)
DROP TABLE IF EXISTS nome_da_tabela;       -- não dá erro se ela não existir
TRUNCATE TABLE itens_pedido;               -- apaga todas as linhas, mantém a estrutura
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `DROP TABLE` | Descartar tabela | Remove estrutura **e** dados |
| `IF EXISTS` | Se existir | Evita erro quando o objeto não existe |
| `TRUNCATE TABLE` | Esvaziar tabela | Remove todas as linhas de uma vez (mais rápido que `DELETE` sem `WHERE`) |

| | `DELETE` (DML) | `TRUNCATE` (DDL) | `DROP` (DDL) |
|-|----------------|------------------|--------------|
| Apaga linhas | Só as do `WHERE` | Todas | Todas |
| Apaga a tabela | Não | Não | **Sim** |
| Tem `WHERE` | Sim | Não | Não |

> ⚠️ `DROP` e `TRUNCATE` são destrutivos. Em produção, faça [backup](13-extras.md) antes.

---

## CREATE INDEX — "o mapa de corredores"

Acelera buscas em colunas muito filtradas, ao custo de mais espaço e escrita mais lenta.

```sql
CREATE INDEX idx_pedidos_cliente ON pedidos (cliente_id);
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `CREATE INDEX` | Criar índice | Cria um "atalho" de busca |
| `ON tabela (coluna)` | Em (tabela e coluna) | Diz qual coluna será indexada |

---

## CREATE VIEW — "consulta salva com nome"

```sql
CREATE VIEW pedidos_pendentes AS
SELECT p.id, c.nome AS cliente, p.data_pedido
FROM pedidos p
JOIN clientes c ON c.id = p.cliente_id
WHERE p.data_entrega IS NULL;

SELECT * FROM pedidos_pendentes;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `CREATE VIEW ... AS` | Criar visão como | Salva um `SELECT` com nome, para reusar como se fosse tabela |
| `JOIN ... ON` | Juntar ... com base em | Liga duas tabelas (ver [tópico 10](10-join-e-union.md)) |
| `IS NULL` | É nulo | Testa se o campo está vazio (pedido ainda não entregue) |

---

[← Cenário de logística](03-cenario-logistica.md) · [Índice](README.md) · [Próximo: SELECT e filtros →](05-select-e-filtros.md)
