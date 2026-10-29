# 02 — Instalação e psql (Linux)

## Entrando no banco

```bash
# entrar como usuário postgres e abrir o terminal do banco
su - postgres
psql
```

```sql
-- criar o banco
CREATE DATABASE logistica;
```

| 🔎 Termo | Português | Para que serve |
|---------|-----------|----------------|
| `CREATE DATABASE` | Criar banco de dados | Cria um novo banco (o "armazém" vazio) |

```bash
# carregar um script .sql dentro de um banco
psql -U postgres -d logistica -f logistica.sql
```

| 🔎 Opção | Português | Para que serve |
|---------|-----------|----------------|
| `-U` | *User* (usuário) | Quem está se conectando |
| `-d` | *Database* (banco) | Em qual banco executar |
| `-f` | *File* (arquivo) | Qual script `.sql` rodar |

> No curso usamos o banco de exemplo **Northwind** (clientes, pedidos, produtos, fornecedores).
> Ele é "logística disfarçada": ver [tópico 14](14-northwind-curso.md).

## Prompt do psql

| Prompt | Significa |
|--------|-----------|
| `#` (no shell) | Você está no terminal do Linux |
| `=#` | Você está dentro do psql como superusuário |
| `=>` | Dentro do psql como usuário comum |
| `-#` | O psql está esperando você terminar o comando (faltou o `;`) |

## Meta-comandos do psql (começam com `\`, **sem** `;`)

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

[← Conceitos](01-conceitos.md) · [Índice](README.md) · [Próximo: Cenário de logística →](03-cenario-logistica.md)
