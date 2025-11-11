# Endpoints de partidas API

Esta coleção contém endpoints para gerenciar partidas (matches) — criar partidas, listar, obter, atualizar, apagar e controlar inscrições e trocas de time. Todos os endpoints requerem autenticação JWT via header `Authorization: Bearer <token>` (os handlers usam middleware `auth`).  

**Base URL:** `http://localhost:4000`

## Guia de Uso Geral

- Utilize os endpoints para criar, atualizar, listar, obter e deletar partidas, além de endpoints para inscrever/desinscrever jogadores e trocar de time (switch).
- Sempre envie o token JWT no header `Authorization`.
- Os esquemas de validação são aplicados nos endpoints (Zod): verifique formato de UUIDs, tamanho mínimo de campos e paginação (limit/page).

## Endpoints disponíveis

- **Create_match:** Cria uma nova partida.
- **Get_match:** Busca uma partida por id.
- **List_matches:** Lista partidas (paginação).
- **Update_match:** Atualiza uma partida existente.
- **Delete_match:** Remove uma partida.
- **Subscribe:** Inscrever usuário em uma partida.
- **Unsubscribe:** Remover inscrição do usuário.
- **Switch_team:** Troca o usuário de time dentro da inscrição.

---

## POST Create_Match

```
POST http://localhost:4000/match/
```

**Descrição:**  
Cria uma partida com os dados fornecidos e retorna os dados da partida criada.

**Headers obrigatórios:**
- `Authorization: Bearer <token>`

**Body (JSON) - campos principais (validação via Zod presente em match.validation.ts):**

- `userId` (string, UUID) — **obrigatório** — id do autor/usuário que cria a partida.
- `title` (string) — **obrigatório**, mínimo 3 caracteres.
- `description` (string) — opcional/mínimo 2 caracteres quando presente.
- `maxPlayers` (int) — **obrigatório**, número máximo de jogadores.
- `MatchDate` (DateTime / ISO8601) — **obrigatório** — data da partida.
- `location` (string) — **obrigatório**.
- `teamNameA` (string) — opcional, default `TIME_A`.
- `teamNameB` (string) — opcional, default `TIME_B`.

**Respostas esperadas:**

- `201 Created`: Retorna o objeto de Match (id, title, description, authorId, maxPlayers, MatchDate, MatchStatus, location, createdAt, updatedAt, players[] etc.).
- `400 Bad Request`: campos obrigatórios ausentes ou inválidos.
- `401 Unauthorized`: token JWT ausente/inválido.
- `403 Forbidden`: usuário sem permissão.
- `404 Not Found`: author (user) não encontrado (se aplicável).

**Exemplo de uso:**

Request:
```bash
POST http://localhost:4000/match/
Headers:
  Authorization: Bearer <token>

Body (JSON):
{
  "author": "{ "id": "a8f1b2c3-....", "name": ..., ...}",
  "title": "Pelada de Domingo",
  "description": "Partida amistosa - todos são bem-vindos",
  "maxPlayers": 10,
  "MatchDate": "2025-11-15T10:00:00.000Z",
  "location": "Campo Central"
}
```

Response (201):
```json
{
  "id": "d4e5f6g7-....",
  "title": "Pelada de Domingo",
  "description": "Partida amistosa - todos são bem-vindos",
  "author": "{ "id": "a8f1b2c3-....", "name": ..., ...}",
  "authorId": "a8f1b2c3-....",
  "maxPlayers": 10,
  "players": [],
  "MatchDate": "2025-11-15T10:00:00.000Z",
  "MatchStatus": "EM_BREVE",
  "location": "Campo Central",
  "teamNameA": "TIME_A",
  "teamNameB": "TIME_B",
  "createdAt": "2025-10-25T14:10:29.170Z",
  "updatedAt": "2025-10-25T14:10:29.170Z"
}
```

---

## GET Get_Match

```
GET http://localhost:4000/match/:id
```

**Descrição:**  
Retorna os detalhes de uma partida (inclui inscrições/players).

**Parâmetros obrigatórios:**

- `id` (string, path) — id da partida (UUID).

**Respostas esperadas:**

- `200 OK`: Objeto Match com `players` (lista de PlayerSubscription com dados básicos do user).
- `404 Not Found`: partida não encontrada.
- `400 Bad Request`: id inválido (validação de UUID).

**Exemplo de uso:**

```bash
GET http://localhost:4000/match/d4e5f6g7-....
Headers:
  Authorization: Bearer <token>
```

Response (200):
```json
{
  "id": "d4e5f6g7-....",
  "title": "Pelada de Domingo",
  "description": "Partida amistosa - todos são bem-vindos",
  "author": "{ "id": "a8f1b2c3-....", "name": ..., ...}",
  "authorId": "a8f1b2c3-....",
  "maxPlayers": 10,
  "players": [],
  "MatchDate": "2025-11-15T10:00:00.000Z",
  "MatchStatus": "EM_BREVE",
  "location": "Campo Central",
  "teamNameA": "TIME_A",
  "teamNameB": "TIME_B",
  "createdAt": "2025-10-25T14:10:29.170Z",
  "updatedAt": "2025-10-25T14:10:29.170Z"
}
```

---

## GET List_Matches (paginação)

```
GET http://localhost:4000/match/?limit=10&page=1
```

**Descrição:**  
Lista partidas paginadas.

**Query params (validados):**

- `limit` (number): máximo por página, coerção numérica, int positivo, max 50, default (ex.: 10).
- `page` (number): página, int positivo, default (ex.: 1).

**Respostas esperadas:**

- `200 OK`: objeto com lista de partidas, page metadata (total/pages) (implementação específica do repositório).
- `400 Bad Request`: parâmetros inválidos.

**Exemplo de uso:**
```bash
GET http://localhost:4000/match/?limit=5&page=2
Headers:
  Authorization: Bearer <token>
```

---

## PATCH Update_Match

```
PATCH http://localhost:4000/match/:id
```

**Descrição:**  
Atualiza campos da partida. Pelo menos um dos campos editáveis deve ser enviado.

**Headers:** `Authorization: Bearer <token>`

**Parâmetros Path:**
- `id` (UUID) — id da partida.

**Body (opcional):**
- `title` (string), `description` (string), `maxPlayers` (int), `MatchDate` (DateTime), `location` (string), `teamNameA`, `teamNameB` etc.

**Respostas esperadas:**
- `200 OK`: partida atualizada (objeto).
- `400 Bad Request`: validação falhou.
- `401 Unauthorized`: token inválido.
- `403 Forbidden`: usuário não autorizado a editar (ex.: não é o author).
- `404 Not Found`: partida não encontrada.

**Exemplo:**
```bash
PATCH http://localhost:4000/match/d4e5f6g7-....
Headers:
  Authorization: Bearer <token>

Body:
{
  "title": "Pelada - Horário Atualizado",
  "MatchDate": "2025-11-15T11:00:00.000Z"
}
```

Response (201):
```json
{
  "id": "d4e5f6g7-....",
  "title": "Pelada - Horário Atualizado",
  "description": "Partida amistosa - todos são bem-vindos",
  "author": "{ "id": "a8f1b2c3-....", "name": ..., ...}",
  "authorId": "a8f1b2c3-....",
  "maxPlayers": 10,
  "players": [],
  "MatchDate": "2025-11-15T11:00:00.000Z",
  "MatchStatus": "EM_BREVE",
  "location": "Campo Central",
  "teamNameA": "TIME_A",
  "teamNameB": "TIME_B",
  "createdAt": "2025-10-25T14:10:29.170Z",
  "updatedAt": "2025-11-10T12:00:13.170Z"
}
```

---

## DELETE Delete_Match

```
DELETE http://localhost:4000/match/:id
```

**Descrição:**  
Remove uma partida do banco de dados.

**Headers:** `Authorization: Bearer <token>`

**Parâmetros Path:**
- `id` (UUID)

**Respostas esperadas:**
- `204 No Content`: partida deletada com sucesso.
- `404 Not Found`: partida não encontrada.
- `401 Unauthorized`: token inválido.
- `403 Forbidden`: usuário sem permissão para deletar (ex.: não é o autor).

**Exemplo:**
```bash
DELETE http://localhost:4000/match/d4e5f6g7-....
Headers:
  Authorization: Bearer <token>
```

---

## POST Subscribe (inscrição do jogador)

```
POST http://localhost:4000/match/:id/subscribe
```

**Descrição:**  
Inscreve o usuário autenticado em uma partida. O serviço cria um `PlayerSubscription` ligado ao `matchId` e `userId` e com `team` (A ou B).

**Headers:** `Authorization: Bearer <token>`

**Path params:**
- `id` (UUID) — id da partida.

**Body (opcional dependendo da implementação):**
- `team` (string enum: `A` ou `B`) — lado do time desejado (caso suportado).

**Respostas esperadas:**
- `201 Created`: inscrição feita.
- `400 Bad Request`:  dados inválidos
- `401 Unauthorized`: token ausente/inválido.
- `403 Forbidden`: limite de players atingido.
- `404 Not Found`: partida não encontrada.
- `409 Conflict`: se já existe inscrição duplicada.

**Exemplo:**
```bash
POST http://localhost:4000/match/d4e5f6g7-.... /subscribe
Headers:
  Authorization: Bearer <token>

Body:
{
  "team": "A"
}
```

---

## DELETE Unsubscribe

```
DELETE http://localhost:4000/match/:id/unsubscribe
```

**Descrição:**  
Remove a inscrição (PlayerSubscription) do usuário autenticado nessa partida.

**Headers:** `Authorization: Bearer <token>`

**Path params:**
- `id` (UUID) — match id.

**Respostas esperadas:**
- `200 Ok`: inscrição removida com sucesso.
- `404 Not Found`: inscrição ou partida não encontrada.
- `401 Unauthorized`: token ausente/inválido.

**Exemplo:**
```bash
DELETE http://localhost:4000/match/d4e5f6g7-.... /unsubscribe
Headers:
  Authorization: Bearer <token>
```

---

## POST Switch_Team

```
POST http://localhost:4000/match/:id/switch
```

**Descrição:**  
Troca o time da inscrição do usuário (A <-> B). Requer autenticação.

**Headers:** `Authorization: Bearer <token>`

**Path params:**
- `id` (UUID) — match id.

**Respostas esperadas:**
- `200 OK`: troca de time realizada com sucesso.
- `404 Not Found`: inscrição ou partida não encontrada.
- `401 Unauthorized`: token inválido.
- `400 Bad Request`: erro na troca (por regras do jogo).

**Exemplo:**
```bash
POST http://localhost:4000/match/d4e5f6g7-.... /switch
Headers:
  Authorization: Bearer <token>
```