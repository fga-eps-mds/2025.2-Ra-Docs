# Endpoints de Grupo API
Esta seção documenta os endpoints relacionados à funcionalidade de grupos na API do Aton.

Aqui está a documentação dos endpoints de **Follow** (Seguir Grupos), formatada conforme solicitado (sem tabelas).

-----
# Endpoints de Follow (Group Follow)

Esta coleção gerencia o relacionamento de "seguir" entre Usuários e Grupos. Permite que usuários sigam grupos para receber atualizações e listar quais grupos um usuário específico está seguindo.

Os endpoints de escrita (`POST`, `DELETE`) requerem autenticação JWT. O endpoint de leitura (`GET`) pode ser público ou autenticado, dependendo da regra de privacidade (neste exemplo, assumimos público para visualização de perfil).

**Base URL:** `http://localhost:4000`

-----

## 1\. Ações de Follow (Escrita)

### POST Follow Group

Faz com que o usuário autenticado comece a seguir um grupo específico.

```http
POST follow/groups/:id/follow
```

**Headers:** `Authorization: Bearer <token>`

**Parâmetros de Rota (obrigatório):**

  - `id` (string): O UUID do **grupo** que se deseja seguir.

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
  - `404 Not Found`: O grupo com o ID fornecido não existe.
  - `409 Conflict`: O usuário já segue este grupo (duplicidade).

-----

### DELETE Unfollow Group

Faz com que o usuário autenticado deixe de seguir um grupo.

```http
DELETE follow/groups/:id/follow
```

**Headers:** `Authorization: Bearer <token>`

**Parâmetros de Rota (obrigatório):**

  - `id` (string): O UUID do **grupo** que se deseja deixar de seguir.

**Exemplo de Response (204 No Content):**

  - A resposta não contém corpo (vazia). O status `204` indica sucesso.

**Respostas de Erro:**

  - `400 Bad Request`: O usuário tentou deixar de seguir um grupo que ele **não** seguia.
  - `401 Unauthorized`: Token não fornecido ou inválido.
  - `404 Not Found`: Grupo não encontrado (opcional, dependendo da implementação do middleware/service).

-----

## 2\. Consultas (Leitura)

### GET List User Following

Retorna a lista de todos os grupos que um determinado usuário está seguindo, com paginação.

```http
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