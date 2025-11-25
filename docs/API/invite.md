# Endpoints da API de convites Grupos <-> Usuários

Este documento descreve os endpoints para gerenciar convites (GroupJoinRequest) — criar, listar, obter, atualizar, apagar e aceitar/recusar convites. Todos os endpoints pressupõem autenticação JWT via header `Authorization: Bearer <token>`.

**Base URL:** `http://localhost:4000`

## Guia de Uso Geral

- Envie o token JWT no header `Authorization`.
- Validações (Zod) aplicam-se aos endpoints: verifique formatos de UUID, datas e tamanhos mínimos de campos.

## Endpoints disponíveis

- **Create_invite:** Cria um convite.
- **Get_invite:** Obtém um convite por id.
- **List_invites:** Lista convites.
- **List_invites_group:** Lista todos os convites de um grupo.
- **List_invites_user:** Lista todos os convites de um usuário.
- **Update_invite:** Atualiza um convite.
- **Delete_invite:** Deleta um convite.

**Obs.:** **List_invites_user** e **List_invites_group:** podem ser filtrados por `sender = group/user`, permitindo encontrar convites enviados por um grupo ou usuário, qualquer valor diferente para `sender` resultura na listagem de todos os convites.

---

## POST Create_Invite

```
POST http://localhost:4000/invite/
```

**Descrição:**  
Cria um convite com os dados fornecidos e retorna o objeto criado.

**Headers obrigatórios:**

- `Authorization: Bearer <token>`

**Body (JSON) - campos comuns (validação esperada):**

- `userId` (string, UUID) — **obrigatório** — id do usuário.
- `groupId` (string, UUID) — **obrigatório** — id do grupo.
- `madeBy` (string, "USER" ou "GROUP") — **obrigatório** — usado para indicar quem enviou o convite
- `message` (string) — **opcional** — mensagem adicional.

**Respostas esperadas:**

- `201 Created`: Retorna o objeto Invite (id, fromUserId, toUserId, status, createdAt, expiresAt, etc.).
- `400 Bad Request`: campos inválidos.
- `404 Not Found`: usuário ou grupo relacionado não encontrado.
- `409 Conflict`: convite duplicado existente.

**Exemplo de uso:**

Request:

```bash
POST http://localhost:4000/invite/

Body (JSON):
{
  "userId": "a8f1b2c3-....",
  "groupId": "b1c2d3e4-....",
  "madeBy": "USER",
  "message": "Vem jogar domingo!"
}
```

Response (201):

```json
{
  "id": "f1e2d3c4-....",
  "userId": "a8f1b2c3-....",
  "groupId": "b1c2d3e4-....",
  "madeBy": "USER",
  "message": "Vem jogar domingo!",
  "status": "PENDING",
  "createdAt": "2025-10-25T14:10:29.170Z",
  "updatedAt": "2025-10-25T14:10:29.170Z"
}
```

## GET Get_Invite

```
GET http://localhost:4000/invite/:id
```

**Descrição:**  
Retorna os detalhes completos do convite (incluindo remetente, destinatário e status).

**Parâmetros path:**

- `id` (string, path) — id do convite (UUID).

**Respostas esperadas:**

- `200 OK`: Objeto Invite completo.
- `404 Not Found`: convite não encontrado.
- `400 Bad Request`: id inválido (validação UUID).

**Exemplo de uso:**

Request:
 
```bash
GET http://localhost:4000/invite/f1e2d3c4-....
```

Response(200)

```json
{
  "id": "f1e2d3c4-....",
  "userId": "a8f1b2c3-....",
  "groupId": "b1c2d3e4-....",
  "madeBy": "USER",
  "message": "Vem jogar domingo!",
  "status": "PENDING",
  "createdAt": "2025-10-25T14:10:29.170Z",
  "updatedAt": "2025-10-25T14:10:29.170Z"
}
```

## GET List_Invites

```
GET http://localhost:4000/invite/
```

**Descrição:**  
Lista todos os convites existentes.

**Respostas esperadas:**

- `200 OK`: objeto com `data` (lista de convites).

Request:
 
```bash
GET http://localhost:4000/invite/
```

Response(200)

```json
[
  {
    "id": "f1e2d3c4-....",
    "userId": "a8f1b2c3-....",
    "groupId": "b1c2d3e4-....",
    "madeBy": "USER",
    "message": "Vem jogar domingo!",
    "status": "PENDING",
    "createdAt": "2025-10-25T14:10:29.170Z",
    "updatedAt": "2025-10-25T14:10:29.170Z"
  }
  // outros convites...
]
```

## GET List_Invites_groups

```
GET http://localhost:4000/invite/:sender/group/:id
```

**Descrição:**  
Lista todos os convites de um determinado grupo, podendo ser filtrado por `sender`.

**Parâmetros path:**

- `id` (string, path) — id do convite (UUID).

**Respostas esperadas:**

- `200 OK`: objeto com `data` (lista de convites).

Request:
 
```bash
GET http://localhost:4000/invite/all/group/b1c2d3e4-....
```

Response(200)

```json
[
  {
    "id": "f1e2d3c4-....",
    "userId": "a8f1b2c3-....",
    "groupId": "b1c2d3e4-....",
    "madeBy": "USER",
    "message": "Vem jogar domingo!",
    "status": "PENDING",
    "createdAt": "2025-10-25T14:10:29.170Z",
    "updatedAt": "2025-10-25T14:10:29.170Z"
  }
  // outros convites do grupo com id=b1c2d3e4-...
]
```

**Obs.:** caso `sender` fosse `group` ou `user`, o GET seria filtrado com base em `madeBy`, pegando somente os convites enviados onde: `madeBy = sender`.

## GET List_Invites_user

```
GET http://localhost:4000/invite/:sender/user/:id
```

**Descrição:**  
Lista todos os convites de um determinado usuário, podendo ser filtrado por `sender`.

**Parâmetros path:**

- `id` (string, path) — id do convite (UUID).

**Respostas esperadas:**

- `200 OK`: objeto com `data` (lista de convites).

Request:
 
```bash
GET http://localhost:4000/invite/all/user/ba8f1b2c3-....
```

Response(200)

```json
[
  {
    "id": "f1e2d3c4-....",
    "userId": "a8f1b2c3-....",
    "groupId": "b1c2d3e4-....",
    "madeBy": "USER",
    "message": "Vem jogar domingo!",
    "status": "PENDING",
    "createdAt": "2025-10-25T14:10:29.170Z",
    "updatedAt": "2025-10-25T14:10:29.170Z"
  }
  // outros convites do user com id=a8f1b2c3-....
]
```

**Obs.:** caso `sender` fosse `group` ou `user`, o GET seria filtrado com base em `madeBy`, pegando somente os convites enviados onde: `madeBy = sender`.

## PATCH Update_Invite

```
PATCH http://localhost:4000/invite/:id
```

**Descrição:**  
Atualiza campos do convite (ex.: message, status).

**Headers:** `Authorization: Bearer <token>`

**Parâmetros path:**

- `id` (string, path) — id do convite (UUID).

**Body (opcional):**

- `message` (string)
- `status` (string)

**Respostas esperadas:**

- `200 OK`: convite atualizado.
- `400 Bad Request`: id invalido.
- `401 Unauthorized`: token inválido.
- `403 Forbidden`: usuário sem permissão para editar.
- `404 Not Found`: convite não encontrado.

Request:
 
```bash
POST http://localhost:4000/invite/f1e2d3c4-....

Body (JSON):
{
  "message": "Vem jogar sabado!"
  "status": "ACCEPTED"
}
```

Response (200):

```json
{
  "id": "f1e2d3c4-....",
  "userId": "a8f1b2c3-....",
  "groupId": "b1c2d3e4-....",
  "madeBy": "USER",
  "message": "Vem jogar sabado!",
  "status": "ACCEPTED",
  "createdAt": "2025-10-25T14:10:29.170Z",
  "updatedAt": "2025-10-25T14:10:29.170Z"
}
```

## DELETE Delete_Invite

```
DELETE http://localhost:4000/invite/:id
```

**Descrição:**  
Remove um convite.

**Headers:** `Authorization: Bearer <token>`

**Params path:**

- `id` (UUID) — id do convite.

**Respostas esperadas:**

- `204 No Content`: convite deletado com sucesso.
- `404 Not Found`: convite não encontrado.
- `401 Unauthorized`: token inválido.

Request: 

```bash
DELETE http://localhost:4000/invite/f1e2d3c4-....
```
Response (204)
``` json
 // No content
```
