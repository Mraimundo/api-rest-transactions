# API REST – Transactions

API REST de transações construída com Fastify, TypeScript, Knex e SQLite. Inclui validação de variáveis de ambiente com Zod e migrações de banco de dados.

## Tecnologias

- Node.js + TypeScript
- Fastify
- Knex
- SQLite
- Zod
- dotenv
- ESLint (config Rocketseat)

## Requisitos Funcionais

- [x] O usuário deve poder criar uma nova transação
- [ ] O usuário deve poder obter um resumo da sua conta
- [ ] O usuário deve poder listar todas transações que já ocorreram
- [ ] O usuário deve poder visualizar uma transação única

## Requisitos Não Funcionais

- [x] A transação pode ser do tipo crédito (soma) ou débito (subtrai)
- [ ] Deve ser possível identificarmos o usuário entre as requisições
- [ ] O usuário só pode visualizar transações as quais ele criou

## Pré-requisitos

- Node.js 18+ (recomendado 20+)
- npm 9+

## Instalação

1. Instalar dependências

   ```bash
   npm install
   ```

2. Configurar variáveis de ambiente

   Um arquivo de exemplo já existe:

   ```bash
   cp .env.exemple .env
   ```

   Variáveis suportadas (src/env/index.ts):
   - NODE_ENV: development | production | test (default: production)
   - DATABASE_URL: caminho do arquivo SQLite (ex.: ./db/app.db)
   - PORT: porta do servidor HTTP (default: 3333)

3. Criar/aplicar migrações do banco

   ```bash
   npm run migrate:latest
   ```

   Comandos úteis:
   - Criar nova migração: `npm run migrate:make`
   - Aplicar migrações pendentes: `npm run migrate:latest`
   - Reverter última migration: `npm run migrate:rollback`

## Execução

Ambiente de desenvolvimento (com reload):

```bash
npm run dev
```

Servidor inicia por padrão em http://localhost:3333

## Scripts disponíveis

- dev: inicia o servidor em modo watch (tsx)
- knex: executa o CLI do Knex com suporte a TS
- migrate:make: cria uma migration (nome default create-tables)
- migrate:latest: aplica as migrations
- migrate:rollback: desfaz a última migration
- lint: executa ESLint com correções automáticas

## Banco de Dados

- Cliente: SQLite
- Arquivo: definido via `DATABASE_URL` (ex.: ./db/app.db)
- Migrações em `./db/migrations`

Tabelas principais:
- transactions
  - id: uuid (PK)
  - session_id: uuid (opcional, index)
  - title: string
  - amount: decimal (débitos são armazenados como valores negativos)
  - type: enum("credit" | "debit")
  - created_at: timestamp (default now)

## Endpoints

Base URL: `http://localhost:3333`

- POST /transactions
  - Descrição: cria uma nova transação
  - Body JSON:
    ```json
    {
      "title": "Salário",
      "amount": 5000,
      "type": "credit"
    }
    ```
  - Regras:
    - type: "credit" soma ao saldo; "debit" armazena o valor como negativo
  - Respostas:
    - 201 Created (sem corpo)

Exemplo cURL:

```bash
curl -i \
  -X POST http://localhost:3333/transactions \
  -H "Content-Type: application/json" \
  -d '{"title":"Salário","amount":5000,"type":"credit"}'
```

## Estrutura do Projeto (resumo)

```
.
├── db/
│   └── migrations/
├── src/
│   ├── @types/knex.d.ts
│   ├── env/index.ts
│   ├── database.ts
│   ├── routes/transactions.ts
│   └── server.ts
├── knexfile.ts
├── package.json
├── tsconfig.json
└── .env (.env.exemple)
```

## Qualidade de Código

Executar ESLint:

```bash
npm run lint
```

## Notas

- As transações do tipo "debit" são persistidas como valores negativos em `amount`.
- `session_id` já existe no schema da tabela via migration, e poderá ser usado posteriormente para identificação do usuário entre requisições.

## Roadmap

- Listagem de transações do usuário (GET /transactions)
- Detalhe de transação (GET /transactions/:id)
- Resumo de saldo (GET /transactions/summary)
- Autenticação/identificação por sessão (session_id) e escopo de acesso por usuário
