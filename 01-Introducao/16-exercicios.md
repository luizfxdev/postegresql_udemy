# 16 — Exercícios de fixação (logística)

1. Liste os 5 produtos mais caros.
2. Liste clientes do Brasil cujo nome contenha "log" (sem diferenciar maiúsculas).
3. Mostre os pedidos ainda não entregues, do mais antigo ao mais recente.
4. Classifique cada pedido como `Rápido` (até 3 dias), `Normal` (4 a 7) ou `Lento` (mais de 7) usando `CASE`.
5. Descubra o total de frete por país de destino, só dos países com frete acima de 1.000.
6. Liste os clientes que nunca fizeram pedido (`LEFT JOIN`).
7. Reajuste em 5% o preço dos produtos com estoque acima de 100 (faça o `SELECT` antes!).
8. Dentro de uma transação, apague um pedido de teste usando `RETURNING *` e depois faça `ROLLBACK`.

Desafios extras (DDL e DML):

9. **DDL:** crie a tabela `veiculos` (`id`, `placa` única e obrigatória, `capacidade_kg`) e depois adicione a coluna `motorista_id` referenciando `motoristas`.
10. **DML:** cadastre 3 veículos num único `INSERT` e reajuste a capacidade de um deles com `UPDATE ... RETURNING *`.

> Se resolveu os 8 primeiros sem consultar, você domina o básico de PostgreSQL.

---

[← Erros comuns e cola](15-erros-e-cola.md) · [Índice](README.md)
