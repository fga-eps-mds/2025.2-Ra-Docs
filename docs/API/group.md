# Endpoints de Grupo API
Esta seção documenta os endpoints relacionados à funcionalidade de grupos na API do Aton.

-----

# Endpoints de grupos API

Esta coleção contém endpoints para gerenciar grupos — criar grupos, listar, obter, atualizar e apagar. Todos os endpoints requerem autenticação JWT via header `Authorization: Bearer <token>` (os handlers usam middleware `auth`).

**Base URL:** `http://localhost:4000`

## Guia de Uso Geral

- Utilize os endpoints para criar, atualizar, listar, obter e deletar grupos.
- Sempre envie o token JWT no header `Authorization`.
- Os esquemas de validação (Zod) validam campos como comprimento mínimo, tipos e enumerações (ex.: status de verificação). Campos principais estão descritos em cada endpoint.

## Endpoints disponíveis

- **Create_group:** Cria um novo grupo.
- **Get_group:** Busca um grupo por `name`.
- **List_groups:** Lista todos os grupos.
- **List_open_groups:** Lista grupos com `acceptingNewMembers = true`.
- **Update_group:** Atualiza um grupo existente (somente administradores do grupo).
- **Delete_group:** Remove um grupo (somente administradores do grupo).

---

## POST Create_Group

```
POST http://localhost:4000/group/
```

**Descrição:**  
Cria um grupo com os dados fornecidos e retorna os dados do grupo criado.

**Headers obrigatórios:**

- `Authorization: Bearer <token>`

**Body (JSON) - campos principais (validação via Zod presente em group.validation.ts):**

- `name` (string) — **obrigatório**, mínimo 2 caracteres.
- `description` (string) — opcional, mínimo 2 caracteres quando presente.
- `sports` (string[]) — opcional — lista de esportes/atividades do grupo.
- `verificationRequest` (boolean) — opcional — se true, o grupo terá `verificationStatus = "PENDING"`.
- `verificationStatus` (string) — opcional — deve ser um dos: `PENDING`, `VERIFIED`, `REJECTED` (validação transforma para uppercase).
- `acceptingNewMembers` (boolean) — opcional — indica se o grupo está aceitando novos membros.

**Respostas esperadas:**

- `201 Created`: Retorna o objeto do Group (id, name, description, authorId, verificationStatus, acceptingNewMembers, createdAt, updatedAt, etc.).
- `400 Bad Request`: campos obrigatórios ausentes ou inválidos.
- `401 Unauthorized`: token JWT ausente/inválido.
- `403 Forbidden`: usuário sem permissão em casos específicos.
- `409 Conflict`: nome do grupo já está em uso.

**Exemplo de uso:**

Request:

```bash
POST http://localhost:4000/group/
Headers:
  Authorization: Bearer <token>

Body (JSON):
{
  "name": "Pesadelo",
  "description": "Altletica das engenharias",
  "sports": ["handbol", "futsal"],
  "verificationRequest": true,
  "acceptingNewMembers": false
}
```

Response (201):

```json
{
  "id": "uuid-do-grupo",
  "name": "Pesadelo",
  "description": "Altletica das engenharias",
  "groupType": "AMATEUR",
  "verificationStatus": "PENDING",
  "acceptingNewMembers": false,
  "sports": ["handbol", "futsal"],
  "createdAt": "2025-10-25T14:10:29.170Z",
  "updatedAt": "2025-10-25T14:10:29.170Z"
}
```

---

## GET Get_Group

```
GET http://localhost:4000/group/:name
```

**Descrição:**  
Retorna os detalhes completos do grupo identificado por `name`, incluindo informações básicas e metadados.

**Parâmetros obrigatórios:**

- `name` (string, path) — nome do grupo.

**Respostas esperadas:**

- `200 OK`: Objeto Group com campos detalhados (incluindo `sports`, `verificationStatus`, `acceptingNewMembers`, possivelmente membros ou contadores conforme implementação).
- `404 Not Found`: grupo não encontrado.
- `400 Bad Request`: parâmetros inválidos.

**Exemplo de uso:**

```bash
GET http://localhost:4000/group/Pesadelo
```

Response (200):

```json
{
  "id": "uuid-do-grupo",
  "name": "Pesadelo",
  "description": "Altletica das engenharias",
  "groupType": "AMATEUR",
  "verificationStatus": "PENDING",
  "acceptingNewMembers": false,
  "sports": ["handbol", "futsal"],
  "createdAt": "2025-10-25T14:10:29.170Z",
  "updatedAt": "2025-10-25T14:10:29.170Z",
  "memberships": [
    /* Membros do grupo */
  ]
}
```

---

## GET List_Groups

```
GET http://localhost:4000/group/
```

**Descrição:**  
Lista todos os grupos (ordenados por `createdAt` decresente).

**Query params:**  
Nenhum obrigatório (implementação atual retorna todos os grupos).

**Respostas esperadas:**

- `200 OK`: array de grupos.

**Exemplo de uso:**

```bash
GET http://localhost:4000/group/

```

Response (200):

```json
[
  {
    "id": "uuid-do-grupo-2",
    "name": "Pelada de domingo",
    "description": "bo jogar um fut?",
    "groupType": "AMATEUR",
    "verificationStatus": null,
    "acceptingNewMembers": true,
    "sports": ["futsal"],
    "createdAt": "2025-11-23T11:11:09.170Z",
    "updatedAt": "2025-11-23T11:11:09.170Z",
    "memberships": [
      /* Membros do grupo */
    ]
  },
  {
    "id": "uuid-do-grupo",
    "name": "Pesadelo",
    "description": "Altletica das engenharias",
    "groupType": "AMATEUR",
    "verificationStatus": "PENDING",
    "acceptingNewMembers": false,
    "sports": ["handbol", "futsal"],
    "createdAt": "2025-10-25T14:10:29.170Z",
    "updatedAt": "2025-10-25T14:10:29.170Z",
    "memberships": [
      /* Membros do grupo */
    ]
  }
]
```

---

## GET List_Open_Groups

```
GET http://localhost:4000/group/open
```

**Descrição:**  
Lista apenas os grupos com `acceptingNewMembers = true`.

**Respostas esperadas:**

- `200 OK`: array de grupos abertos.

**Exemplo:**

```bash
GET http://localhost:4000/group/open
```

Response (200):

```json
[
  {
    "id": "uuid-do-grupo-2",
    "name": "Pelada de domingo",
    "description": "bo jogar um fut?",
    "groupType": "AMATEUR",
    "verificationStatus": null,
    "acceptingNewMembers": true,
    "sports": ["futsal"],
    "createdAt": "2025-11-23T11:11:09.170Z",
    "updatedAt": "2025-11-23T11:11:09.170Z",
    "memberships": [
      /* Membros do grupo */
    ]
  }
]
```

---

## PATCH Update_Group

```
PATCH http://localhost:4000/group/:name
```

**Descrição:**  
Atualiza campos do grupo. Pelo menos um dos campos editáveis deve ser enviado. Somente membros com `role = "ADMIN"` no grupo podem editar (verificação feita pelo serviço com `GroupMembershipRepository`).

**Headers:** `Authorization: Bearer <token>`

**Parâmetros Path:**

- `name` (string) — nome do grupo.

**Body (opcional):**

- `name` (string, min 2) — novo nome.
- `description` (string, min 2) — nova descrição.
- `sports` (string[]) — lista de esportes.
- `verificationRequest` (boolean).
- `verificationStatus` (string) — `PENDING` | `VERIFIED` | `REJECTED`.
- `acceptingNewMembers` (boolean).

**Respostas esperadas:**

- `200 OK`: grupo atualizado (objeto).
- `400 Bad Request`: validação do body falhou.
- `401 Unauthorized`: token inválido.
- `403 Forbidden`: usuário não autorizado para editar (ex.: não é ADMIN).
- `404 Not Found`: grupo não encontrado.

**Exemplo:**

```bash
PATCH http://localhost:4000/group/Pesadelo
Headers:
  Authorization: Bearer <token>

Body:
{
  "verificationStatus": "VERIFIED",
  "acceptingNewMembers": true
}
```

Response (200):

```json
{
  "id": "uuid-do-grupo",
  "name": "Pesadelo",
  "description": "Altletica das engenharias",
  "groupType": "ATHLETIC",
  "verificationStatus": "VERIFIED",
  "acceptingNewMembers": true,
  "sports": ["handbol", "futsal"],
  "createdAt": "2025-10-25T14:10:29.170Z",
  "updatedAt": "2025-10-25T14:10:29.170Z",
  "memberships": [
    /* Membros do grupo */
  ]
}
```

---

## DELETE Delete_Group

```
DELETE http://localhost:4000/group/:name
```

**Descrição:**  
Remove um grupo do banco de dados. Apenas administradores do grupo podem deletá-lo.

**Headers:** `Authorization: Bearer <token>`

**Parâmetros Path:**

- `name` (string) — nome do grupo.

**Respostas esperadas:**

- `204 No Content`: grupo deletado com sucesso.
- `404 Not Found`: grupo não encontrado.
- `401 Unauthorized`: token ausente/inválido.
- `403 Forbidden`: usuário sem permissão para deletar (ex.: não é ADMIN).

**Exemplo:**

```bash
DELETE http://localhost:4000/group/Pelada%20Central
Headers:
  Authorization: Bearer <token>
```

Response (204): _No Content_

---

# Endpoints de Follow (Group Follow)

Esta coleção gerencia o relacionamento de "seguir" entre Usuários e Grupos. Permite que usuários sigam grupos para receber atualizações e listar quais grupos um usuário específico está seguindo.

Os endpoints de escrita (`POST`, `DELETE`) requerem autenticação JWT. O endpoint de leitura (`GET`) pode ser público ou autenticado, dependendo da regra de privacidade (neste exemplo, assumimos público para visualização de perfil).

**Base URL:** `http://localhost:4000`

-----

## 1\. Ações de Follow (Escrita)

### POST Follow Group

Faz com que o usuário autenticado comece a seguir um grupo específico.

```
POST follow/groups/:name/follow
```

**Headers:** `Authorization: Bearer <token>`

**Parâmetros de Rota (obrigatório):**

  - `name` (string): O nome exato do **grupo** que se deseja seguir.

**Body:**

  - Não enviar corpo.

**Exemplo de Response (201 Created):**

```json
{
  "message": "Grupo seguido com sucesso"
}
```

**Respostas de Erro:**

  - `401 Unauthorized`: Token não fornecido ou inválido.
  - `404 Not Found`: O grupo com o nome fornecido não existe.
  - `409 Conflict`: O usuário já segue este grupo (duplicidade).

-----

### DELETE Unfollow Group

Faz com que o usuário autenticado deixe de seguir um grupo.

```
DELETE follow/groups/:name/follow
```

**Headers:** `Authorization: Bearer <token>`

**Parâmetros de Rota (obrigatório):**

  - `name` (string): O nome exato do **grupo** que se deseja deixar de seguir.

**Exemplo de Response (204 No Content):**

  - A resposta não contém corpo (vazia). O status `204` indica sucesso.

**Respostas de Erro:**

  - `400 Bad Request`: O usuário tentou deixar de seguir um grupo que ele **não** seguia.

  - `401 Unauthorized`: Token não fornecido ou inválido.

  - `404 Not Found`: Grupo não encontrado.

-----

## 2\. Consultas (Leitura)

### GET List User Following

Retorna a lista de todos os grupos que um determinado usuário está seguindo, com paginação.

```
GET follow/users/:id/following-groups?limit=10&page=1
```

**Parâmetros de Rota (obrigatório):**

  - `id` (string): O UUID do **usuário** cujo perfil está sendo visualizado.

**Query Params (opcional):**

  - `limit` (number): Itens por página (Padrão: 10).
  - `page` (number): Número da página (Padrão: 1).

**Exemplo de Response (200 OK):**

```json
{
  "data": [
    {
      "id": "uuid-grupo-atletica",
      "name": "Atlética Computação",
      "description": "A maior da UnB",
      "groupType": "ATHLETIC",
      "createdAt": "2025-01-10T10:00:00.000Z",
      "updatedAt": "2025-01-10T10:00:00.000Z"
    },
    {
      "id": "uuid-grupo-futsal",
      "name": "Futsal Terça-Feira",
      "description": "Racha semanal",
      "groupType": "AMATEUR",
      "createdAt": "2025-02-15T18:30:00.000Z",
      "updatedAt": "2025-02-15T18:30:00.000Z"
    }
  ],
  "meta": {
    "page": 1,
    "limit": 10,
    "totalCount": 2,
    "totalPages": 1,
    "hasNextPage": false,
    "hasPrevPage": false
  }
}
```

**Respostas de Erro:**

  - `400 Bad Request`: Parâmetros de paginação inválidos.
  - `404 Not Found`: Usuário não encontrado (opcional, se validado).
