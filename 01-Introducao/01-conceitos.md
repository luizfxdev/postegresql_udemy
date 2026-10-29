# 01 — Conceitos

## O que é PostgreSQL?

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

## Mapa mental — os grupos de comandos

```
SQL
├── DDL  (estrutura)   CREATE · ALTER · DROP · TRUNCATE      → construir/reformar o armazém
├── DML  (dados)       INSERT · UPDATE · DELETE              → movimentar as caixas
├── DQL  (consulta)    SELECT                                → consultar o inventário
├── DCL  (permissões)  GRANT · REVOKE                        → quem tem a chave de qual setor
└── TCL  (transação)   BEGIN · COMMIT · ROLLBACK             → "tudo ou nada"
```

| Sigla | Significa | Em português | Logística |
|-------|-----------|--------------|-----------|
| DDL | Data Definition Language | Linguagem de **Definição** de Dados | Montar corredores e prateleiras |
| DML | Data Manipulation Language | Linguagem de **Manipulação** de Dados | Receber, corrigir e descartar caixas |
| DQL | Data Query Language | Linguagem de **Consulta** de Dados | Conferir o que há no estoque |
| DCL | Data Control Language | Linguagem de **Controle** de Dados | Liberar/bloquear acesso ao setor |
| TCL | Transaction Control Language | Linguagem de **Controle de Transações** | Confirmar ou cancelar uma operação completa |

**Memorize:** *CRUD* = **C**reate (`INSERT`) · **R**ead (`SELECT`) · **U**pdate (`UPDATE`) · **D**elete (`DELETE`).

---

[← Índice](README.md) · [Próximo: Instalação e psql →](02-instalacao-e-psql.md)
