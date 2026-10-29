# 01 — Introdução ao PostgreSQL (Logística Edition)

> Guia para **entender e memorizar**. Tema: uma operação de logística
> (clientes pedem, produtos saem do armazém, motoristas entregam).
> Dica de estudo: leia a analogia, rode o exemplo, depois feche o arquivo e tente reescrever.

## Como ler as queries

Abaixo de cada query há uma tabela **🔎 Tradução** com os termos SQL usados nela:
o termo em inglês, a tradução em português e **para que ele serve**.

## Índice

| # | Tópico | O que você aprende |
|---|--------|--------------------|
| 01 | [Conceitos](01-conceitos.md) | O que é PostgreSQL, termos básicos, grupos de comandos (DDL, DML, DQL, DCL, TCL) |
| 02 | [Instalação e psql](02-instalacao-e-psql.md) | Entrar no banco, prompt, meta-comandos |
| 03 | [Cenário de logística](03-cenario-logistica.md) | Modelo de dados, tipos, constraints |
| 04 | [DDL — estrutura](04-ddl.md) | `CREATE`, `ALTER`, `DROP`, `TRUNCATE`, índices e views |
| 05 | [SELECT e filtros](05-select-e-filtros.md) | `SELECT`, `WHERE`, operadores lógicos, `NULL` |
| 06 | [ORDER BY e LIMIT](06-order-by-e-limit.md) | Ordenar e cortar resultados |
| 07 | [Transformando resultados](07-transformando-resultados.md) | `CASE`, `COALESCE`, `CAST`, datas |
| 08 | [Texto (strings)](08-strings.md) | `LIKE`, `UPPER`, `CONCAT`, `SUBSTRING`... |
| 09 | [Números e agregações](09-numeros-e-agregacoes.md) | `SUM`, `AVG`, `GROUP BY`, `HAVING` |
| 10 | [JOIN e UNION](10-join-e-union.md) | Ligar tabelas e empilhar resultados |
| 11 | [DML — manipulação de dados](11-dml.md) | `INSERT`, `UPDATE`, `DELETE`, `RETURNING` |
| 12 | [Transações](12-transacoes.md) | `BEGIN`, `COMMIT`, `ROLLBACK`, ACID |
| 13 | [Extras](13-extras.md) | Subconsulta, `EXPLAIN`, backup |
| 14 | [Ponte com o curso (Northwind)](14-northwind-curso.md) | Carregar o banco do curso e mapa das aulas |
| 15 | [Erros comuns e cola](15-erros-e-cola.md) | Armadilhas, cheat sheet, frases para memorizar |
| 16 | [Exercícios](16-exercicios.md) | 8 desafios de fixação |

## Referências

- Documentação oficial: <https://www.postgresql.org/docs/current/>
- ORDER BY: <https://www.postgresql.org/docs/current/queries-order.html>
- CAST: <https://www.postgresql.org/docs/current/sql-createcast.html>
- Funções de texto: <https://www.postgresql.org/docs/current/functions-string.html>
- Banco de exemplo Northwind: <https://github.com/pthom/northwind_psql>
