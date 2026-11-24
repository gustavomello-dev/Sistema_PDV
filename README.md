# Sistema PDV - Restaurante

Sistema de PDV (Point of Sale) para restaurante com fluxo completo:

`Garçom abre pedido -> Cozinha prepara -> Garçom entrega -> Caixa recebe pagamento`

## Stack

- **Frontend:** Angular 18 (standalone components)
- **Backend:** Node.js + Express (ESM)
- **Banco:** MongoDB + Mongoose
- **Segurança:** JWT, bcrypt, Helmet, CORS, rate limit e validação Zod
- **Perfis:** ADMIN, MANAGER, WAITER (garçom), KITCHEN (cozinha), CASHIER (caixa)

## Estrutura

```
pdv-sistema/
├── backend/       # API Node.js + Express + MongoDB
├── frontend/      # Aplicação Angular
└── docker-compose.yml
```

## Como rodar

### 1. Subir o MongoDB

```bash
docker compose up -d
```

### 2. Backend

```bash
cd backend
cp .env.example .env
npm install
npm run seed
npm run dev
```

API: http://localhost:3000/api

### 3. Frontend

```bash
cd frontend
npm install
npm start
```

Aplicação: http://localhost:4200

## Usuários iniciais

| Perfil | E-mail | Senha |
|---|---|---|
| Administrador | admin@pdv.com | Admin@123 |
| Gerente | gerente@pdv.com | Gerente@123 |
| Garçom | garcom@pdv.com | Garcom@123 |
| Cozinha | cozinha@pdv.com | Cozinha@123 |
| Caixa | caixa@pdv.com | Caixa@123 |

> Troque todas as senhas em produção. O backend usa `bcrypt` com salt e nunca retorna o hash ao cliente.

## Fluxo do pedido

1. Garçom seleciona uma mesa livre e cria o pedido.
2. Garçom adiciona produtos, quantidade e observações.
3. Garçom envia o pedido para a cozinha (`SENT`).
4. Cozinha inicia o preparo (`PREPARING`) e marca como pronto (`READY`).
5. Garçom confirma a entrega ao cliente (`DELIVERED`).
6. Caixa registra pagamentos parciais ou totais (Dinheiro, Cartão ou Pix).
7. Quando o total é quitado, o pedido vira `PAID` e é encerrado.

## Segurança implementada

- Senhas armazenadas apenas como hash bcrypt.
- JWT assinado com segredo do ambiente e expiração.
- Middleware de autenticação em todas as rotas protegidas.
- RBAC por papel: apenas perfis autorizados acessam cada operação.
- Rate limit no login para dificultar brute force.
- Helmet para cabeçalhos de segurança.
- Validação de entrada com Zod.
- CORS configurável por variável de ambiente.
- Senhas e tokens nunca são retornados nas respostas da API.
- Status do pedido controlado por transições válidas no backend.

## Variáveis de ambiente

```env
PORT=3000
MONGO_URI=mongodb://localhost:27017/pdv
JWT_SECRET=troque-este-segredo-em-producao
JWT_EXPIRES_IN=8h
CORS_ORIGIN=http://localhost:4200
```
