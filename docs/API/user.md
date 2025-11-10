# Endpoints de Usuário API
Este documento descreve os endpoints disponíveis para gerenciar usuários na API do Aton, detalhando suas funcionalidades, métodos HTTP, parâmetros e exemplos de uso.

-----

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