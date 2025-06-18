## 🧪 Desafio Técnico – Dev Node.js (Back-End) | Marlim

### 🧭 Objetivo

Criar uma API REST chamada **MarlimPay**, que simula um sistema de transações financeiras entre usuários. O foco é avaliar seu domínio com:

* Boas práticas de backend
* APIs REST seguras e performáticas
* Idempotência, webhooks, rate limit
* Banco de dados (preferência NoSQL, , mas SQL também é aceito)
* Estrutura próxima de ambientes serverless

---

## 📚 Entidades principais

* **Usuários** (`users`)
* **Transações** (`transactions`)

## 🔐 Autenticação

Mocke a autenticação com um `Authorization: Bearer <token>` e considere o `user_id` associado ao token para simular operações por usuário.

---

## 👤 Endpoints de Usuários

### **POST /users**

Criação de novo usuário.

**Headers obrigatórios:**

```http
Authorization: Bearer <mocked_token>
```

**Body esperado:**

```json
{
  "name": "Maria Silva",
  "email": "maria@example.com",
  "balance": 1000.00
}
```

✅ Deve:

* Validar campos obrigatórios (`name`, `email`, `balance`)
* Garantir que o e-mail não seja duplicado

**Resposta 201:**

```json
{
  "user_id": "user_abc123",
  "name": "Maria Silva",
  "email": "maria@example.com",
  "balance": 1000.00
}
```

--

### **GET /users/\:id**

Consulta de usuário por ID.

**Headers obrigatórios:**

```http
Authorization: Bearer <mocked_token>
```

✅ Deve:

* Retornar 404 se o `user_id` não existir

**Resposta 200:**

```json
{
  "user_id": "user_abc123",
  "name": "Maria Silva",
  "email": "maria@example.com",
  "balance": 1000.00
}
```

--

### **PUT /users/\:id**

Atualização de nome e e-mail do usuário.

**Headers obrigatórios:**

```http
Authorization: Bearer <mocked_token>
```

**Body esperado:**

```json
{
  "name": "Maria da Silva",
  "email": "mariads@example.com"
}
```

✅ Deve:

* Permitir atualizar apenas `name` e `email`
* Não permitir alterar o `balance`
* Retornar 404 se o usuário não existir

**Resposta 200:**

```json
{
  "user_id": "user_abc123",
  "name": "Maria da Silva",
  "email": "mariads@example.com",
  "balance": 1000.00
}
```

---

## 💸 Endpoints de Transações

### **POST /transactions**

Criação de transação entre dois usuários.

**Headers obrigatórios:**

```http
Authorization: Bearer <mocked_token>
Idempotency-Key: 7a98a3f2-56c8-4dbe-902b-abc123
```

**Body esperado:**

```json
{
  "payer_id": "user_123",
  "receiver_id": "user_456",
  "amount": 150.75
}
```

✅ Deve:

* Verificar se `payer` tem saldo suficiente
* Implementar **idempotência** via `Idempotency-Key`
* Criar transação com status `"pending"`
* Deduzir valor do saldo de `payer` no momento da criação

**Resposta 201:**

```json
{
  "transaction_id": "txn_abc123",
  "status": "pending",
  "created_at": "2025-06-06T15:00:00Z"
}
```

🛑 Respostas de erro:

* 400 se dados inválidos
* 409 se `Idempotency-Key` já usado
* 429 se exceder o rate limit (veja abaixo)

--

### **POST /webhook/payment-gateway**

Simula confirmação de pagamento por parte de um gateway.

**Headers obrigatórios:**

```http
Authorization: Bearer <mocked_token>
```

**Body esperado:**

```json
{
  "transaction_id": "txn_abc123",
  "status": "approved" // ou "failed"
}
```

✅ Deve:

* Atualizar status da transação para `approved` ou `failed`
* Em caso de `failed`, devolver o valor ao `payer`

**Resposta 200:**

```json
{
  "message": "Webhook recebido com sucesso"
}
```

--

### **GET /transactions/\:id**

Consulta uma transação por ID.

**Headers obrigatórios:**

```http
Authorization: Bearer <mocked_token>
```

✅ Deve:

* Retornar 404 se transação não existir

**Resposta 200:**

```json
{
  "transaction_id": "txn_abc123",
  "payer_id": "user_123",
  "receiver_id": "user_456",
  "amount": 150.75,
  "status": "approved",
  "created_at": "2025-06-06T15:00:00Z"
}
```

--

### **GET /users/\:user\_id/transactions**

Lista todas as transações relacionadas a um usuário.

**Headers obrigatórios:**

```http
Authorization: Bearer <mocked_token>
```

✅ Deve:

* Listar transações onde o usuário é pagador ou recebedor

**Resposta 200:**

```json
[
  {
    "transaction_id": "txn_abc123",
    "direction": "sent", // ou "received"
    "amount": 150.75,
    "status": "approved"
  },
  {
    "transaction_id": "txn_xyz456",
    "direction": "received",
    "amount": 320.00,
    "status": "pending"
  }
]
```

---

## 📊 Rate Limit

✅ Deve:

* Limitar para **5 transações por minuto por `user_id`**
* Retornar:

```json
{
  "error": "Rate limit exceeded"
}
```

* Código HTTP: `429 Too Many Requests`

---

## ☁️ Serverless (requisito técnico)

✅ Estrutura o projeto de forma compatível com serverless:

* Separação por `handler`, `services`, `routes`
* Pode ser executado como Lambda / Firebase Function

---

## 🧠 Bônus (opcional)

* Autenticação mockada com `JWT` ou `API Key`
* Logger (padrão info/error/debug)
* Documentação via Swagger ou Postman

---

## 📦 Entrega

* Repositório público no GitHub
* `README.md` com:

  * Como rodar o projeto localmente
  * Como testar os endpoints
  * Descrição das decisões técnicas (ex.: idempotência, rate limit, etc.)

Quando finalizar, conceda acesso ao projeto com permissões de **Editor** para o e-mail **[alex@marlim.co](mailto:alex@marlim.co)**.

---

## 🔥 Que tal elevar o nível do desafio? Topa mais essa?

### **Firebase Edition – MarlimPay Serverless**

Se quiser ir além, crie o desafio utilizando o ecossistema **Firebase**, implementando **Firestore**, **Cloud Functions** e **Hosting**.

### ✅ Requisitos adicionais:

### **Firestore**

* Armazene:

  * Usuários (`users`)
  * Transações (`transactions`)
  * Logs de Webhooks recebidos (coleção opcional: `webhook_events`)

--

### **Cloud Functions (Node.js)**

* Crie a API REST completa com **Express**, usando rotas:

  * `POST /users`
  * `GET /users/:id`
  * `PUT /users/:id`
  * `POST /transactions`
  * `GET /transactions/:id`
  * `GET /users/:user_id/transactions`
  * `POST /webhook/payment-gateway`

✅ A Cloud Function deve:

* Aplicar todas as validações de negócio descritas no desafio original
* Usar **Firestore SDK** nativamente
* Implementar **idempotência, rate limit e autenticação mockada**

--

### **Hosting**

* Suba uma **documentação simples** da API (HTML ou Markdown renderizado):

  * Pode ser estática (ex: gerada por Swagger UI ou Redoc)
  * Deve estar disponível publicamente via URL da CDN do Firebase Hosting
  * Ideal: incluir payloads de exemplo e explicações básicas dos endpoints

--

**⚠️ Importante: A versão Firebase deve funcionar localmente no emulador**. Certifique-se de que seja possível rodar tudo com ```firebase emulators:start```.
