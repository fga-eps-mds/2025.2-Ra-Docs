# Endpoints de partidas API
Este documento descreve os endpoints disponíveis na API de Partidas do Aton, detalhando suas funcionalidades, métodos HTTP, parâmetros e exemplos de uso.

## Módulo: Partidas (Match)

Este módulo gerencia a visualização e interação com as partidas da plataforma.

### 1\. Listar Todas as Partidas

Retorna uma lista de todas as partidas. Este endpoint é otimizado para o feed e **não** inclui a lista detalhada de jogadores, apenas a contagem de vagas.

`GET /match`

  * **Autenticação:** Nenhuma (Rota Pública).

#### Success Response (`200 OK`)

Retorna um *array* de objetos de partida. Cada objeto inclui os campos calculados `spots` (vagas) e `isSubscriptionOpen` (se pode se inscrever).

**Exemplo de Resposta:**

```json
[
  {
    "id": "uuid-partida-1",
    "title": "Futsal da Computação vs. Elétrica",
    "description": "Amistoso de abertura",
    "date": "2025-11-10T19:00:00.000Z",
    "location": "Quadra Padrão UnB",
    "maxPlayers": 10,
    "createdAt": "...",
    "updatedAt": "...",
    "spots": {
      "filled": 5,
      "open": 5
    },
    "isSubscriptionOpen": true,
    "players": []
  },
  {
    "id": "uuid-partida-2",
    "title": "Vôlei de Teste",
    "description": null,
    "date": "2025-11-11T20:00:00.000Z",
    "location": "Ginásio 2",
    "maxPlayers": 2,
    "createdAt": "...",
    "updatedAt": "...",
    "spots": {
      "filled": 2,
      "open": 0
    },
    "isSubscriptionOpen": false,
    "players": []
  }
]
```
**OBS**: O array `players` sempre será vazio nesse endpoint, pois isso torna o `GET` lento. Esse `GET` deve ser usado para o feed. Para um `GET` mais aprofundado, utilize o `/match/:id`.
-----

### 2\. Buscar Partida Específica

Retorna os detalhes completos de *uma* única partida, incluindo a lista de todos os jogadores inscritos.

`GET /match/:id`

  * **Autenticação:** Nenhuma (Rota Pública).

#### URL Parameters

| Parâmetro | Tipo | Obrigatório | Descrição |
| :--- | :--- | :--- | :--- |
| `id` | string | Sim | O ID (UUID) da partida que deseja buscar. |

#### Success Response (`200 OK`)

Retorna um *objeto* único da partida, contendo a lista (`players`) de inscrições e os dados dos usuários.

**Exemplo de Resposta:**

```json
{
  "id": "uuid-partida-1",
  "title": "Futsal da Computação vs. Elétrica",
  "description": "Teste",
  "date":  "2025-11-11T20:00:00.000Z",
  "location": "A quadra",
  "maxPlayers": 10,
  "createdAt": "...",
  "updatedAt": "...",
  "spots": {
    "filled": 1,
    "open": 9
  },
  "isSubscriptionOpen": true,
  "players": [
    {
      "id": "uuid-inscricao-abc",
      "userId": "uuid-usuario-123",
      "matchId": "uuid-partida-1",
      "createdAt": "...",
      "user": {
        "id": "uuid-usuario-123",
        "name": "Yan Santos",
        "userName": "yansantos"
      }
    }
  ]
}
```

#### Error Responses

| Código | Status | Motivo | Exemplo de Resposta (JSON) |
| :--- | :--- | :--- | :--- |
| `400` | Bad Request | O `:id` fornecido na URL não é um UUID válido. | `{"error": "Erro de validação", "issues": ...}` |
| `404` | Not Found | O UUID é válido, mas nenhuma partida foi encontrada com esse ID. | `{"error": "Partida não encontrada"}` |

-----

### 3\. Inscrever-se em uma Partida

Inscreve o usuário autenticado (via token JWT) na partida especificada.

`POST /match/:id/subscribe`

  * **Autenticação:** **Obrigatória** (Header `Authorization: Bearer <token>`).

#### URL Parameters

| Parâmetro | Tipo | Obrigatório | Descrição |
| :--- | :--- | :--- | :--- |
| `id` | string | Sim | O ID (UUID) da partida na qual se inscrever. |

#### Success Response (`201 Created`)

Retorna uma mensagem de sucesso, indicando que a `PlayerSubscription` foi criada.

**Exemplo de Resposta:**

```json
{
  "message": "Inscrição realizada com sucesso"
}
```

#### Error Responses

| Código | Status | Motivo | Exemplo de Resposta (JSON) |
| :--- | :--- | :--- | :--- |
| `400` | Bad Request | O `:id` não é um UUID válido. | `{"error": "Erro de validação", "issues": ...}` |
| `401` | Unauthorized | O token JWT não foi fornecido ou é inválido. | `{"error": "Token não fornecido"}` |
| `403` | Forbidden | A partida já está cheia (vagas esgotadas). | `{"error": "Não há mais vagas disponíveis..."}` |
| `404` | Not Found | Nenhuma partida foi encontrada com esse ID. | `{"error": "Partida não encontrada"}` |
| `409` | Conflict | O usuário autenticado já está inscrito nesta partida. | `{"error": "Você já está inscrito nesta partida"}` |

-----

### 4\. Cancelar Inscrição da Partida

Remove a inscrição do usuário autenticado (via token JWT) da partida especificada.

`DELETE /match/:id/unsubscribe`

  * **Autenticação:** **Obrigatória** (Header `Authorization: Bearer <token>`).

#### URL Parameters

| Parâmetro | Tipo | Obrigatório | Descrição |
| :--- | :--- | :--- | :--- |
| `id` | string | Sim | O ID (UUID) da partida da qual cancelar a inscrição. |

#### Success Response (`200 OK`)

**Exemplo de Resposta:**

Retorna uma mensagem sinalizando que a `PlayerSubscription` foi removida com sucesso.

```json
{
  "message": "Inscrição cancelada com sucesso"
}
```
#### Error Responses

| Código | Status | Motivo | Exemplo de Resposta (JSON) |
| :--- | :--- | :--- | :--- |
| `400` | Bad Request | O `:id` não é um UUID válido. | `{"error": "Erro de validação", "issues": ...}` |
| `401` | Unauthorized | O token JWT não foi fornecido ou é inválido. | `{"error": "Token não fornecido"}` |
| `404` | Not Found | O usuário *não estava inscrito* nesta partida (ou a partida não existe). | `{"error": "Você não está inscrito nesta partida"}` |