# 03 — Nosso cenário de logística

Todos os exemplos deste guia usam as tabelas abaixo (o `CREATE TABLE` completo está no
[tópico 04 — DDL](04-ddl.md)).

```
categorias ──< produtos >── fornecedores
                  │
clientes ──< pedidos >── motoristas
               │
          itens_pedido >── produtos
```

`──<` lê-se "um para muitos": **um** cliente tem **muitos** pedidos; **um** pedido tem **muitos** itens.

| Tabela | Papel na operação |
|--------|-------------------|
| `clientes` | Quem compra e recebe |
| `fornecedores` | Quem abastece o armazém |
| `categorias` | Agrupamento dos produtos (ex.: alimentos, eletrônicos) |
| `produtos` | O que está na prateleira |
| `motoristas` | Quem entrega (com `supervisor_id` apontando para outro motorista) |
| `pedidos` | A ordem de entrega (cliente, motorista, datas, frete) |
| `itens_pedido` | O que vai dentro de cada pedido |

## Tipos de dados mais usados

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

## Constraints (as regras da casa)

| Constraint | Garante | Na logística |
|-----------|---------|--------------|
| `PRIMARY KEY` | Único e não nulo | Código de rastreio nunca repete |
| `FOREIGN KEY` / `REFERENCES` | Só aponta para algo que existe | Pedido só de cliente cadastrado |
| `NOT NULL` | Obrigatório | Todo produto tem preço |
| `UNIQUE` | Sem repetição | Uma placa por veículo |
| `DEFAULT` | Valor padrão | Frete começa em 0 |
| `CHECK` | Regra própria | `quantidade > 0` |

---

[← Instalação e psql](02-instalacao-e-psql.md) · [Índice](README.md) · [Próximo: DDL →](04-ddl.md)
