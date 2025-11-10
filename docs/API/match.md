# Endpoints de partidas API
Este documento descreve os endpoints disponíveis na API de Partidas do Aton, detalhando suas funcionalidades, métodos HTTP, parâmetros e exemplos de uso.

-----

## Módulo: Partidas (Match) **READER**

Este módulo gerencia a visualização e interação com as partidas da plataforma, incluindo um sistema de alocação de times (Time A vs. Time B).

-----

### 1\. Listar Partidas (Feed Paginado)

Retorna uma lista paginada de todas as partidas disponíveis. Esta rota é otimizada para o feed (scroll infinito) e **não** inclui a lista detalhada de jogadores, apenas a contagem total de vagas.

`GET /match`

  * **Autenticação:** Nenhuma (Rota Pública).

#### Parâmetros

| Parâmetro | Tipo | Padrão | Descrição |
| :--- | :--- | :--- | :--- |
| `limit` | number | 10 | Quantidade de itens por página (Máx: 50). |
| `page` | number | 1 | O número da página a ser buscada. |

#### Success Response (`200 OK`)

Retorna um objeto de paginação (`data` e `meta`). O `MatchStatus` é **calculado dinamicamente**: se a `MatchDate` já passou e o status no banco ainda for `EM_BREVE`, a API retornará `EM_ANDAMENTO`.

**Exemplo de Resposta:**

```json
{
  "data": [
    {
      "id": "uuid-partida-1",
      "title": "Futsal da Eng.",
      "description": "Amistoso das engenharias",
      "authorId": "uuid-do-autor",
      "MatchDate": "2025-11-10T17:30:00.000Z",
      "MatchStatus": "EM_ANDAMENTO",
      "location": "Quadra Padrão UnB",
      "maxPlayers": 20,
      "teamNameA": "Time Legal",
      "teamNameB": "Time Ruim",
      "createdAt": "2025-11-09T10:00:00.000Z",
      "updatedAt": "2025-11-09T10:00:00.000Z",
      "isSubscriptionOpen": true,
      "spots": {
        "filled": 5,
        "open": 15
      }
    }
    // ... (mais partidas)
  ],
  "meta": {
    "page": 1,
    "limit": 10,
    "totalCount": 12,
    "totalPages": 2,
    "hasNextPage": true,
    "hasPrevPage": false
  }
}
```

**Error Responses:**
| Código | Status | Motivo |
| :--- | :--- | :--- |
| `400` | Bad Request | Erro de validação do Zod (ex: `limit` maior que 50). |

-----

### 2\. Buscar Partida Específica (Detalhe)

Retorna os detalhes completos de *uma* única partida, incluindo os nomes customizados dos times (`teamA`, `teamB`) e a lista de todos os jogadores inscritos em cada time.

`GET /match/:id`

  * **Autenticação:** Nenhuma (Rota Pública).

#### Parâmetro

| Parâmetro | Tipo | Obrigatório | Descrição |
| :--- | :--- | :--- | :--- |
| `id` | string | Sim | O ID (UUID) da partida que deseja buscar. |

#### Success Response (`200 OK`)

Retorna um *objeto* único da partida. O `MatchStatus` é calculado dinamicamente.

**Exemplo de Resposta:**

```json
{
  "id": "uuid-partida-1",
  "title": "Futsal",
  "description": "Descrição",
  "authorId": "uuid-do-autor",
  "MatchDate": "2025-11-10T17:30:00.000Z",
  "MatchStatus": "EM_ANDAMENTO",
  "location": "Quadra",
  "maxPlayers": 20,
  "teamNameA": "Time A",
  "teamNameB": "Time B",
  "createdAt": "2025-11-09T10:00:00.000Z",
  "updatedAt": "2025-11-09T10:00:00.000Z",
  "isSubscriptionOpen": true,
  "spots": {
    "totalMax": 20,
    "totalFilled": 1
  },
  "teamA": {
    "name": "Time A",
    "max": 10,
    "filled": 1,
    "isOpen": true,
    "players": [
      {
        "id": "uuid-usuario-123",
        "name": "Yan Santos",
        "userName": "yansantos"
      }
    ]
  },
  "teamB": {
    "name": "Time B",
    "max": 10,
    "filled": 0,
    "isOpen": true,
    "players": []
  }
}
```

#### Error Responses

| Código | Status | Motivo | Exemplo de Resposta (JSON) |
| :--- | :--- | :--- | :--- |
| `400` | Bad Request | O `:id` fornecido na URL não é um UUID válido. | `{"error": "Erro de validação", "issues": ...}` |
| `404` | Not Found | Nenhuma partida foi encontrada com esse ID. | `{"error": "Partida não encontrada"}` |



#### Error Responses

| Código | Status | Motivo | Exemplo de Resposta (JSON) |
| :--- | :--- | :--- | :--- |
| `400` | Bad Request | O `:id` fornecido na URL não é um UUID válido. | `{"error": "Erro de validação", "issues": ...}` |
| `404` | Not Found | Nenhuma partida foi encontrada com esse ID. | `{"error": "Partida não encontrada"}` |

-----

### 3\. Inscrever-se em uma Partida (Round Robin)

Inscreve o usuário autenticado na partida. O usuário **não escolhe o time**. O servidor aloca o usuário automaticamente usando a lógica **Round Robin**:

1.  O usuário é alocado no time com menos jogadores.
2.  Em caso de empate, o usuário é alocado no **Time A**.

`POST /match/:id/subscribe`

  * **Autenticação:** **Obrigatória** (Header `Authorization: Bearer <token>`).

#### URL Parameters

| Parâmetro | Tipo | Obrigatório | Descrição |
| :--- | :--- | :--- | :--- |
| `id` | string | Sim | O ID (UUID) da partida na qual se inscrever. |

#### Success Response (`201 Created`)

```json
{
  "message": "Inscrição realizada com sucesso"
}
```

#### Error Responses

| Código | Status | Motivo | Exemplo de Resposta (JSON) |
| :--- | :--- | :--- | :--- |
| `401` | Unauthorized | Token JWT não fornecido ou inválido. | `{"error": "Token não fornecido"}` |
| `403` | Forbidden | A partida está cheia (ambos os times lotados). | `{"error": "Partida cheia. Não há vagas em..."}` |
| `404` | Not Found | Partida não encontrada com esse ID. | `{"error": "Partida não encontrada"}` |
| `409` | Conflict | O usuário autenticado já está inscrito nesta partida. | `{"error": "Você já está inscrito nesta partida"}` |

-----

### 4\. Trocar de Time

Permite que um usuário já inscrito troque do seu time atual para o time oposto (ex: A -\> B ou B -\> A).

`POST /match/:id/switch`

  * **Autenticação:** **Obrigatória** (Header `Authorization: Bearer <token>`).

#### URL Parameters

| Parâmetro | Tipo | Obrigatório | Descrição |
| :--- | :--- | :--- | :--- |
| `id` | string | Sim | O ID (UUID) da partida. |

#### Success Response (`200 OK`)

```json
{
  "message": "Troca de time realizada com sucesso"
}
```

#### Error Responses

| Código | Status | Motivo | Exemplo de Resposta (JSON) |
| :--- | :--- | :--- | :--- |
| `401` | Unauthorized | Token JWT não fornecido ou inválido. | `{"error": "Token não fornecido"}` |
| `403` | Forbidden | O time de destino está cheio. | `{"error": "O Time B está cheio. Não é possível trocar."}` |
| `404` | Not Found | O usuário autenticado não está inscrito nesta partida. | `{"error": "Você não está inscrito nesta partida"}` |

-----

### 5\. Cancelar Inscrição da Partida

Remove a inscrição do usuário autenticado da partida.

`DELETE /match/:id/unsubscribe`

  * **Autenticação:** **Obrigatória** (Header `Authorization: Bearer <token>`).

#### URL Parameters

| Parâmetro | Tipo | Obrigatório | Descrição |
| :--- | :--- | :--- | :--- |
| `id` | string | Sim | O ID (UUID) da partida. |

#### Success Response (`200 OK`)

```json
{
  "message": "Inscrição cancelada com sucesso"
}
```

#### Error Responses

| Código | Status | Motivo | Exemplo de Resposta (JSON) |
| :--- | :--- | :--- | :--- |
| `401` | Unauthorized | Token JWT não fornecido ou inválido. | `{"error": "Token não fornecido"}` |
| `404` | Not Found | O usuário não estava inscrito nesta partida. | `{"error": "Você não está inscrito nesta partida"}` |