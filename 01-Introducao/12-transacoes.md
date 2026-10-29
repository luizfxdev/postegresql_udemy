# 12 — Transações: "tudo ou nada"

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

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `BEGIN` | Começar | Abre a transação: o que vier depois ainda não é definitivo |
| `UPDATE ... SET ... WHERE` | Atualizar ... definir ... onde | Baixa 5 unidades do estoque do produto 10 |
| `INSERT INTO ... VALUES` | Inserir em ... valores | Registra o item no pedido |
| `COMMIT` | Confirmar | Grava **tudo** de forma permanente |
| `ROLLBACK` | Desfazer | Cancela **tudo** desde o `BEGIN` |

## Propriedades ACID

| Letra | Nome | Em português | Na logística |
|-------|------|--------------|--------------|
| **A** | Atomicidade | Tudo ou nada | Baixou estoque **e** criou o pedido, ou nenhum dos dois |
| **C** | Consistência | Regras sempre válidas | Estoque nunca fica incoerente com as regras (`CHECK`, `FK`) |
| **I** | Isolamento | Transações não se atropelam | Dois operadores não vendem a mesma última caixa |
| **D** | Durabilidade | Confirmou, está salvo | Após o `COMMIT`, nem queda de energia perde o pedido |

## Ensaio seguro de um DELETE

```sql
BEGIN;
DELETE FROM pedidos WHERE id = 999 RETURNING *;    -- veja o que foi apagado
SELECT * FROM pedidos WHERE id = 999;              -- confirme que sumiu
ROLLBACK;                                          -- e volta tudo como era
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `DELETE FROM ... WHERE` | Apagar de ... onde | Remove o pedido de teste |
| `RETURNING *` | Retornando tudo | Mostra o que foi removido |
| `ROLLBACK` | Desfazer | Cancela a exclusão: nada foi perdido |

Dica: dentro de `BEGIN`, teste o `DELETE`/`UPDATE`, confira com `SELECT` e só então `COMMIT`.

---

[← DML](11-dml.md) · [Índice](README.md) · [Próximo: Extras →](13-extras.md)
