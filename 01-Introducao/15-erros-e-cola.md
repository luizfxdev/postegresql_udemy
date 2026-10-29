# 15 — Erros comuns e cola relâmpago

## Erros comuns (e como evitar)

| Erro | Causa | Solução |
|------|-------|---------|
| Esquecer o `;` | Prompt vira `-#` e fica esperando | Digite `;` e Enter |
| `WHERE x = NULL` | NULL não se compara com `=` | `IS NULL` |
| `UPDATE`/`DELETE` sem `WHERE` | Afeta tudo | Rotina segura do [tópico 11](11-dml.md) |
| Coluna fora do `GROUP BY` | Mistura agregada e não agregada | Inclua no `GROUP BY` |
| Aspas duplas no valor | `"x"` é nome de coluna | Texto vai em aspas **simples**: `'x'` |
| `AND`/`OR` sem parênteses | Precedência inesperada (`AND` vem antes de `OR`) | Use `( )` |
| `FLOAT` para dinheiro | Arredondamento impreciso | `NUMERIC(10,2)` |
| Confiar na ordem sem `ORDER BY` | Ordem não é garantida | Sempre declare |
| `SELECT *` em tudo | Lento e frágil | Liste as colunas |
| `DROP`/`TRUNCATE` sem backup | Sem volta | [Backup](13-extras.md) ou transação |
| Concatenar texto em SQL vindo de usuário | **SQL Injection** | Use parâmetros (`PreparedStatement`) |

---

## Cola relâmpago (cheat sheet)

```sql
-- DDL (estrutura)
CREATE TABLE t (id SERIAL PRIMARY KEY, nome VARCHAR(100) NOT NULL);
ALTER TABLE t ADD COLUMN c TEXT;
DROP TABLE t;

-- CONSULTAR
SELECT col1, col2 FROM tabela WHERE cond GROUP BY col HAVING cond ORDER BY col DESC LIMIT 10;

-- JUNTAR
SELECT ... FROM a JOIN b ON a.id = b.a_id;
SELECT ... UNION [ALL] SELECT ...;

-- DML (dados)
INSERT INTO t (c1, c2) VALUES (v1, v2) RETURNING *;
UPDATE t SET c1 = v1 WHERE cond RETURNING *;
DELETE FROM t WHERE cond RETURNING *;

-- TRANSAÇÃO
BEGIN; ... COMMIT;   -- ou ROLLBACK;
```

```
psql:  \l  \c  \dt  \d tabela  \x  \q  \?
```

## Dicionário relâmpago

| Termo | Português | Serve para |
|-------|-----------|------------|
| `SELECT` | Selecionar | Escolher colunas |
| `FROM` | De | Escolher a tabela |
| `WHERE` | Onde | Filtrar linhas |
| `GROUP BY` | Agrupar por | Agrupar linhas |
| `HAVING` | Tendo | Filtrar grupos |
| `ORDER BY` | Ordenar por | Ordenar |
| `LIMIT` | Limite | Cortar quantidade |
| `AND` / `OR` / `NOT` | E / Ou / Não | Combinar ou inverter condições |
| `IN` / `BETWEEN` / `LIKE` | Em / Entre / Como | Lista, intervalo, padrão de texto |
| `IS NULL` | É nulo | Testar vazio |
| `JOIN ... ON` | Juntar ... em | Ligar tabelas |
| `UNION` | União | Empilhar resultados |
| `INSERT INTO ... VALUES` | Inserir em ... valores | Criar linha |
| `UPDATE ... SET` | Atualizar ... definir | Alterar linha |
| `DELETE FROM` | Apagar de | Remover linha |
| `CREATE` / `ALTER` / `DROP` | Criar / Alterar / Descartar | Estrutura |
| `BEGIN` / `COMMIT` / `ROLLBACK` | Começar / Confirmar / Desfazer | Transação |

## Frases para memorizar

1. **`WHERE` filtra linhas, `HAVING` filtra grupos.**
2. **`NULL` só se testa com `IS`.**
3. **Sem `WHERE`, `UPDATE` e `DELETE` mexem em tudo.**
4. **`UNION` empilha linhas, `JOIN` junta colunas.**
5. **Texto entre aspas simples, nomes entre aspas duplas.**
6. **Antes de alterar: `SELECT`. Depois de alterar: `SELECT`.**
7. **Dinheiro é `NUMERIC`.**
8. **DDL mexe na estrutura, DML mexe nos dados.**

---

[← Ponte com o curso](14-northwind-curso.md) · [Índice](README.md) · [Próximo: Exercícios →](16-exercicios.md)
