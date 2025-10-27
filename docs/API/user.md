# Endpoints de Usuário API
Este documento descreve os endpoints disponíveis para gerenciar usuários na API do Aton, detalhando suas funcionalidades, métodos HTTP, parâmetros e exemplos de uso.

-----


## Módulo: Autenticação (Login)

Este módulo gerencia a autenticação e o login de usuários.

### 1\. Login de Usuário

Autentica um usuário com e-mail e senha. Em caso de sucesso, retorna um token JWT para ser usado em rotas protegidas, junto com os dados do usuário e quaisquer avisos (como a necessidade de definir um perfil).

`POST localhost:4000/login`

-----

### Request Body

O corpo da requisição deve ser um JSON contendo o e-mail e a senha do usuário.

**Exemplo:**

```json
{
  "email": "usuario@exemplo.com",
  "password": "minhasenha123"
}
```

**Campos:**

| Campo | Tipo | Obrigatório | Descrição |
| :--- | :--- | :--- | :--- |
| `email` | string | Sim | O e-mail de cadastro do usuário. Deve ter um formato válido. |
| `password` | string | Sim | A senha do usuário. (Mínimo 1 caractere). |

-----

### Success Response (`200 OK`)

Retorna um objeto com três chaves:

1.  `token`: O token de acesso JWT.
2.  `user`: Um objeto com as informações básicas do usuário.
3.  `warns`: Um array de strings contendo avisos. Este array **incluirá `"Configuração de perfil pendente."`** se o `profileType` do usuário for `null`, sinalizando ao frontend que o usuário deve ser redirecionado para a tela de escolha de perfil.

**Exemplo (Usuário com perfil pendente):**

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNseGFiY2RlMTIzIiwiZXhwIjoxNjk4NDI4MjIyfQ.exemploToken",
  "user": {
    "id": "clxabcde123",
    "name": "Nome do Usuário",
    "email": "usuario@exemplo.com"
  },
  "warns": [
    "Configuração de perfil pendente."
  ]
}
```

**Exemplo (Usuário com perfil já definido):**

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNseGFiY2RlMTIzIiwiZXhwIjoxNjk4NDI4MjIyfQ.exemploToken",
  "user": {
    "id": "clxabcde123",
    "name": "Nome do Usuário",
    "email": "usuario@exemplo.com"
  },
  "warns": []
}
```

-----

### Error Responses

O endpoint trata os erros e retorna mensagens claras.

| Código | Status | Motivo | Exemplo de Resposta (JSON) |
| :--- | :--- | :--- | :--- |
| `400` | Bad Request | Erro de validação do Zod (ex: e-mail inválido, senha não enviada). | `{"error": "Erro de validação", "issues": { ... }}` |
| `401` | Unauthorized | O e-mail fornecido não foi encontrado no banco de dados. | `{"error": "E-mail não encontrado."}` |
| `401` | Unauthorized | A senha fornecida não corresponde ao hash no banco de dados. | `{"error": "E-mail ou senha incorretos."}` |
| `500` | Internal Server | Erro raro (ex: o usuário existe, mas não possui `passwordHash`). | `{"error": "Conta de usuário inválida."}` |

## Módulo: Usuários

Este módulo gerencia o CRUD de usuários.

### 1\. Cadastro de Novo Usuário

Registra um novo usuário na plataforma.

`POST localhost:4000/users`

**Nota Importante:** Conforme a regra de negócio do `user.service.ts`, o `profileType` **não** é enviado nesta rota. Ele é automaticamente definido como `NULL` no banco de dados. O usuário será solicitado a escolher um perfil no primeiro login (o endpoint de Login retornará um "aviso" sobre isso).

-----

### Request Body

O corpo da requisição é validado pelo `createUserSchema` e deve conter os seguintes campos:

**Exemplo:**

```json
{
  "name": "Nome",
  "userName": "meu_username",
  "email": "usuario@exemplo.com",
  "password": "senhaSegura123"
}
```

**Campos:**

| Campo | Tipo | Obrigatório | Descrição (Regras do `user.validation.ts`) |
| :--- | :--- | :--- | :--- |
| `name` | string | Sim | Nome do usuário (mínimo 2 caracteres). |
| `userName` | string | Sim | Nome de usuário único (mínimo 1 caractere). |
| `email` | string | Sim | E-mail único do usuário (deve ter formato válido). |
| `password` | string | Sim | Senha do usuário (mínimo 6 caracteres). |

-----

### Success Response (`201 Created`)

Em caso de sucesso, a API retorna o objeto completo do novo usuário (sem a senha). Note que o `profileType` é `null`, conforme a regra de negócio.

**Exemplo:**

```json
{
  "id": "clxzyxw456",
  "name": "Nome Sobrenome",
  "email": "usuario@exemplo.com",
  "userName": "meu_username",
  "profileType": null,
  "createdAt": "2025-10-27T17:30:00.000Z",
  "updatedAt": "2025-10-27T17:30:00.000Z"
}
```

-----

### Error Responses

| Código | Status | Motivo | Exemplo de Resposta (JSON) |
| :--- | :--- | :--- | :--- |
| `400` | Bad Request | **Erro de Validação (Zod):** Os dados enviados não seguem as regras (ex: senha curta, e-mail inválido, campos faltando). | `{"error": "Erro de validação", "issues": { ... }}` |
| `400` | Bad Request | **Conflito (Service):** O `email` enviado já está cadastrado no banco de dados. | `{"error": "Email já cadastrado"}` |
| `400` | Bad Request | **Conflito (Service):** O `userName` (não verificado no seu service atual, mas pode ser adicionado) já está cadastrado. | `{"error": "Username já cadastrado"}` |