# Endpoints de partidas API
Este documento descreve os endpoints disponíveis na API de Partidas do Aton, detalhando suas funcionalidades, métodos HTTP, parâmetros e exemplos de uso.

-----

## Módulo: Partidas (Match) **READER**

Este módulo gerencia a visualização e interação com as partidas da plataforma, incluindo um sistema de alocação de times (Time A vs. Time B).

-----

### 1\. Listar Todas as Partidas (Feed)

Retorna uma lista *otimizada* de todas as partidas para o feed. Esta rota é projetada para ser rápida e **não** inclui os detalhes de cada time ou a lista de jogadores.

`GET /match`

  * **Autenticação:** Nenhuma (Rota Pública).

#### Success Response (`200 OK`)

Retorna um *array* de partidas. O objeto `spots` mostra a contagem total, e o booleano `isSubscriptionOpen` informa ao frontend se ainda há vagas (em *qualquer* um dos times).

**Exemplo de Resposta:**

```json
[
  {
    "id": "uuid-partida-1",
    "title": "Futsal da Computação vs. Elétrica",
    "date": "2025-11-10T19:00:00.000Z",
    "location": "Quadra Padrão UnB",
    "maxPlayers": 20,
    "teamNameA": "Camisas",
    "teamNameB": "Peles",
    "createdAt": "...",
    "updatedAt": "...",
    "isSubscriptionOpen": true,
    "spots": {
      "filled": 5,
      "open": 15
    }
  },
  {
    "id": "uuid-partida-2",
    "title": "Vôlei de Teste",
    "date": "2025-11-11T20:00:00.000Z",
    "location": "Ginásio 2",
    "maxPlayers": 12,
    "teamNameA": "Time A",
    "teamNameB": "Time B",
    "createdAt": "...",
    "updatedAt": "...",
    "isSubscriptionOpen": false,
    "spots": {
      "filled": 12,
      "open": 0
    }
  }
]
```

-----

### 2\. Buscar Partida Específica (Detalhe)

Retorna os detalhes completos de *uma* única partida, incluindo os nomes customizados dos times e a lista de jogadores inscritos em cada um.

`GET /match/:id`

  * **Autenticação:** Nenhuma (Rota Pública).

#### URL Parameters

| Parâmetro | Tipo | Obrigatório | Descrição |
| :--- | :--- | :--- | :--- |
| `id` | string | Sim | O ID (UUID) da partida que deseja buscar. |

#### Success Response (`200 OK`)

Retorna um *objeto* único da partida, contendo a estrutura `teamA` e `teamB` com suas respectivas vagas e listas de jogadores.

**Exemplo de Resposta:**

```json
{
  "id": "uuid-partida-1",
  "title": "Futsal da Computação vs. Elétrica",
  "description": "Amistoso de abertura",
  "date": "2025-11-10T19:00:00.000Z",
  "location": "Quadra Padrão UnB",
  "maxPlayers": 20,
  "teamNameA": "Camisas",
  "teamNameB": "Peles",
  "createdAt": "...",
  "updatedAt": "...",
  "isSubscriptionOpen": true,
  "spots": {
    "totalMax": 20,
    "totalFilled": 1
  },
  "teamA": {
    "name": "Camisas",
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
    "name": "Peles",
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