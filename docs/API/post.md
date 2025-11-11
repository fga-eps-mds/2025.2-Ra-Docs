# Endpoint de Posts/eventos API
Esta coleção contém endpoints para gerenciar posts em um sistema de Feed de eventos e informações por parte publisher. Todos os endpoints requerem autenticação JWT via header `Authorization: Bearer` .

**Base URL:** `http://localhost:4000`

## Guia de Uso Geral

- Utilize os endpoints para criar, atualizar e deletar postagens, juntamente com o endpoint para deletar comentarios dentro de post criados pelo usuario.
- Sempre envie o token JWT no header Authorization.
- Consulte os exemplos de uso em cada endpoint para facilitar a integração.

## Endpoints disponíveis

- **List_posts:** Retorna uma lista paginada de posts.
- **Get_post_by_id:** Retorna um post específico pelo seu ID.
- **Create_post:** Cria um novo post.
- **Update_post:** Atualiza as informações de um post já criado.
- **Delete_post:** Deleta um post.
- **Delete_comment:** Deleta um comentario dentro de um post criado pelo usuario.

Consulte a documentação detalhada em cada endpoint para informações de parâmetros, respostas e exemplos de uso.

## GET List_posts (Feed)

```
http://localhost:4000/post/
```

**Descrição:**
Retorna uma lista paginada de posts, ordenados do mais recente para o mais antigo.

**Parâmetros opcionais:**

- limit (number): Número máximo de posts por página.
  - Padrão: 10
  - Máximo permitido: 50

- page (number): O número da página a ser retornada.
  - Padrão: 1

**Parâmetros de resposta (Data):**

- Retorna uma lista de objetos Post (semelhante ao retorno de Get_post_by_id, mas sem inclusão de comments, likes, etc., no post.repository.ts anexo, retorna apenas os campos básicos do Post).

**Parâmetros de Resposta (Meta - Paginação):**

- page (number): O número da página atual.
- limit (number): O limite de itens por página.
- totalCount (number): O número total de posts no sistema.
- totalPages (number): O número total de páginas disponíveis.
- hasNextPage (boolean): Indica se há uma próxima página.
- hasPrevPage (boolean): Indica se há uma página anterior.

**Respostas esperadas:**

- 200 OK: Retorna um objeto contendo a lista de posts em data e os metadados de paginação em meta.
- 400 Bad Request: limit maior que 50 ou parâmetros de query inválidos.
- 401 Unauthorized: Token JWT ausente ou inválido.

**Exemplo de uso:**

```bash
GET http://localhost:4000/post/?limit=20&page=2
```
**Guia de uso:**
- Use este endpoint para buscar o feed de posts. Você pode controlar a quantidade de posts e a página usando os parâmetros limit e page.

### Exemplo:

#### Request:

```
curl --location 'http://localhost:4000/post/?limit=5&page=1' \
--header 'Authorization: Bearer user:5b308629-cf63-433e-8334-666697f8cdd6'
```

#### Response:

```
{
    "data": [
        {
            "id": "5b308629-cf63-433e-8334-666697f8cdd6",
            "title": "Post Mais Recente",
            "content": "Conteúdo do post mais recente.",
            "type": "GENERAL",
            "author": "gabro",
            "authorId": "id-gabro",
            "group": "pesadelo",
            "groupId": "id-pesadelo",
            "createdAt": "2025-10-25T14:10:29.170Z",
            "updatedAt": "2025-10-25T14:10:29.170Z",
            "eventDate": null,
            "eventFinishDate": null,
            "location": null,
            "likesCount": 5,
            "commentsCount": 2,
            "attendancesCount": 0
        },
        // ... mais 4 posts ...
    ],
    "meta": {
        "page": 1,
        "limit": 5,
        "totalCount": 42,
        "totalPages": 9,
        "hasNextPage": true,
        "hasPrevPage": false
    }
}
```

## GET Get_post_by_id

```
http://localhost:4000/post/:id/
```
**Descrição:**
- Retorna os detalhes de um post específico, incluindo informações selecionadas de comentários associados.

**Parâmetros obrigatórios(Path):**

- id (string, path): Identificador único da postagem.
- Parâmetros de Resposta (Inclusões):
- comments (Comment[]): Lista dos comentários da postagem, ordenados por data de criação (createdAt: "desc").
- Cada comentário inclui: id, content, createdAt e o author (com id e userName).

**Respostas esperadas:**

- 200 OK: Retorna o objeto Post completo com as inclusões.
- 400 Bad Request: ID inválido (não é um UUID).
- 404 Not Found: Postagem não encontrada com o ID fornecido.
- 401 Unauthorized: Token JWT ausente ou inválido.

**Exemplo de uso:**

```
Bash

GET http://localhost:4000/post/5b308629-cf63-433e-8334-666697f8cdd6
Headers: Authorization: Bearer <token>
Guia de uso: Utilize este endpoint para obter todas as informações de uma única postagem.
```

### Exemplo:

#### Request

```
curl --location 'http://localhost:4000/post/5b308629-cf63-433e-8334-666697f8cdd6' \
--header 'Authorization: Bearer user:5b308629-cf63-433e-8334-666697f8cdd6'
```

#### Response

```
(JSON)

{
    "id": "5b308629-cf63-433e-8334-666697f8cdd6",
    "title": "Pesadelo X Cruzeiro",
    "content": "Detalhes sobre o evento do jogo.",
    "type": "EVENT",
    "author": "gabro",
    "authorId": "id-gabro",
    "group": "pesadelo",
    "groupId": "id-pesadelo",
    "createdAt": "2025-10-25T14:10:29.170Z",
    "updatedAt": "2025-10-25T14:10:29.170Z",
    "eventDate": "2025-10-25T10:00:00.170Z",
    "eventFinishDate": "2025-10-25T14:00:00.170Z",
    "location": "Estádio Principal",
    "likesCount": 5,
    "commentsCount": 2,
    "attendancesCount": 10,
    "comments": [
        {
            "id": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d",
            "content": "Que notícia incrível!",
            "createdAt": "2025-10-25T14:30:00.000Z",
            "author": {
                "id": "user-comment-id",
                "userName": "Comentador1"
            }
        },
        // ... outros comentários ...
    ],
    "likes": {},
    "reports": {},
    "attendances": {}
}
```

## POST Create_Post

```
http://localhost:4000/post/
```

**Descrição:**  
Cria um post com os dados fornecidos e retorna todas as informações dele

**Parâmetros obrigatórios:**

- id (string): Identificador único da postagem

- title (string): titulo da postagem
- type (string): tipo de postagem
- author (User): username do autor responsavel pela criação da postagem
- authorId (string): id do autor

**Parâmetros opicionais:**

- content (string): conteúdo da postagem

- group (Group): grupo responsavel pela criação da postagem
- groupId (string): id do grupo

**Parâmetros especificos:**

- `type: EVENT`

  - eventDate (DateTime): data de inicio do evento

  - eventFinishDate (DateTime): data de termino do evento
  - location (string): localização do evento

**Parârametros denormalizados/contadores**

- likesCount (int): número de curtidas em uma postagem

- commentsCount (int): número de comentarios em uma postagem
- attendancesCount (int): número de presenças "confiramadas" em uma postagem

todos os contadores acima são inicializados como 0 por padrão.

**Parâmetros anexados:**

- comments (Comment[]): lista com os comentarios presentes na postagem

- likes (PostLike[]): lista com os usuarios que curtiram a postagme
- reports (Report[]): lista das denuncias feitas sobre a postagem
- attendences (Attendence[]): lista dos usuários com presenças "confirmadas" no evento (valido somente para `type: EVENT`)

**Respostas esperadas:**

- `201 Created`: Retorna um objeto com os parametros da postagem.

- `400 Bad Request`: campos obrigatorios ausentes ou invalidos
- `401 Unathorized`: Token JWT ausente ou inválido.
- `403 Forbidden`: usuario ou grupo não possui autorização para continuar
- `404 Not Found`: Author da postagem não encontrado

**Exemplo de uso:**

```bash
POST http://localhost:4000/post/
```

```
Body (JSON):
{
 "title": "Pesadelo X Cruzeiro",
 "type": "GENERAL",
 "author": "gabro",
 "authorId": "id-gabro",
 "group": "pesadelo",
 "groupId": "id-pesadelo"
}

```

**Guia de uso:**  
Utilize este endpoint criar uma postagem. Certifique-se que os campos necessarios estejam preenchidos corretamente.

### exemplo:

#### Request

```
curl --location '/feed' \
--data-raw '{
    "title": "Pesadelo X Cruzeiro",
    "type": "EVENT",
    "author": "Gabro",
    "authorId": "id-gabro",
    "group": "pesadelo",
    "groupId": "id-pesadelo",
    "eventDate": "2025-10-25T10:00:00.170Z",
    "eventFinishDate": "2025-10-25T14:00:00.170Z"
}'
```

#### Response

```
{
        "id": "5b308629-cf63-433e-8334-666697f8cdd6",
        "title": "Pesadelo X Cruzeiro",
        "type": "EVENT",
        "author": "gabro",
        "authorId": "id-gabro",
        "group": "pesadelo",
        "groupId": "id-pesadelo",
        "createdAt": "2025-10-25T14:10:29.170Z",
        "updatedAt": "2025-10-25T14:10:29.170Z",
        "eventDate": "2025-10-25T10:00:00.170Z",
        "eventFinishDate": "2025-10-25T14:00:00.170Z",
        "likesCount": 0,
        "commentsCount": 0,
        "attendancesCount": 0,
        "comments": {},
        "likes": {},
        "reports": {},
        "attendances": {},
}
```

## PATCH Update_post

```
http://localhost:4000/post/:id/
```

**Descrição:**  
Atualiza as informações de uma postagem existente no banco de dados, identificado pelo id.

**Obs.:** Pelo menos um dos parâmetros abaixo deve estar presente.

**Parâmetros opicionais:**

- title (string): titulo da postagem

- type (string): tipo de postagem
- author (User): username do autor responsavel pela criação da postagem
- authorId (string): id do autor
- content (string): conteúdo da postagem
- group (Group): grupo responsavel pela criação da postagem
- groupId (string): id do grupo

**Parâmetros especificos:**

- `type: EVENT`

  - eventDate (DateTime): data de inicio do evento

  - eventFinishDate (DateTime): data de termino do evento
  - location (string): localização do evento

**Respostas esperadas:**

- `200 OK`: Usuário atualizado com sucesso.

- `400 Bad Request`: campos obrigatórios ausentes ou inválidos.
- `404 Not Found`: Usuário não encontrado.
- `401 Unauthorized`: Token JWT ausente ou inválido.
- `403 Forbidden`: usuario ou grupo não possui autorização para continuar

**Exemplo de uso:**

```bash
PATCH http://localhost:4000/post/5b308629-cf63-433e-8334-666697f8cdd6
```

```
Headers: Authorization: Bearer <token>
Body (JSON):
{
  "title": "Rodrigo se junta ao Cruzeiro",
  "content": "Famoso jogador entra para o time dos sonhos"
}
```

**Guia de uso:**  
Utilize este endpoint para atualizar os dados de uma postagem. Apenas envie os campos que deseja modificar.

### exemplo:

#### Request

```
curl --location --request PATCH '/feed/5b308629-cf63-433e-8334-666697f8cdd6' \
--header 'Authorization: Bearer user:5b308629-cf63-433e-8334-666697f8cdd6' \
--data '{
    "title": "Rodrigo se junta ao Cruzeiro",
    "content": "Famoso jogador entra para o time dos sonhos"
}'
```

#### Response

```
{
    "id": "5b308629-cf63-433e-8334-666697f8cdd6",
    "title": "Rodrigo se junta ao Cruzeiro",
    "content": "Famoso jogador entra para o time dos sonhos"
    "type": "EVENT",
    "author": "gabro",
    "authorId": "id-gabro",
    "group": "pesadelo",
    "groupId": "id-pesadelo",
    "createdAt": "2025-10-25T14:10:29.170Z",
    "updatedAt": "2025-10-25T14:10:29.170Z",
    "eventDate": "2025-10-25T10:00:00.170Z",
    "eventFinishDate": "2025-10-25T14:00:00.170Z",
    "likesCount": 0,
    "commentsCount": 0,
    "attendancesCount": 0,
    "comments": {},  
    "likes": {},  
    "reports": {},  
    "attendances": {},  
}
```

## DELETE Delete_post

```
http://localhost:4000/post/:id/ 
```

**Descrição:**  
Remove uma postagem do banco de dados utilizando o id.

**Parâmetros obrigatórios:**

- id (string, path): id da postagem a ser removido.

**Respostas esperadas:**

- `204 No Content`: postagem removido com sucesso.

- `404 Not Found`: postagem não encontrado.
- `401 Unauthorized`: Token JWT ausente ou inválido.
- `403 Forbidden`: usuario ou grupo não possui autorização para continuar

**Exemplo de uso:**

```bash
DELETE http://localhost:4000/feed/5b308629-cf63-433e-8334-666697f8cdd6
Headers: Authorization: Bearer <token>
```

**Guia de uso:**  
Utilize este endpoint para excluir postagens do sistema. Certifique-se de que a postagem realmente deve ser removido, pois esta ação é irreversível.

#### Request

```
curl --location --request DELETE '/feed/5b308629-cf63-433e-8334-666697f8cdd6' \
--header 'Authorization: Bearer user:5b308629-cf63-433e-8334-666697f8cdd6'
```

#### Response

```
// Não retorna nada
```

## DELETE Delete_comment

```
http://localhost:4000/comment/:id/
```

**Descrição:**  
Remove um comentario da postagem do banco de dados utilizando o id.

**Parâmetros obrigatórios:**

- id (string, path): id do comentario a ser removido.

**Respostas esperadas:**

- `204 No Content`: comentario removido com sucesso.

- `404 Not Found`: comentario ou postagem não encontrados.
- `401 Unauthorized`: Token JWT ausente ou inválido.
- `403 Forbidden`: usuario ou grupo não possui autorização para continuar

**Exemplo de uso:**

```bash
DELETE http://localhost:4000/feed/5b308629-cf63-433e-8334-666697f8cdd6
Headers: Authorization: Bearer <token>
```

**Guia de uso:**  
Utilize este endpoint para excluir comentarios de uma postagem do sistema. Certifique-se de que o comentario realmente deve ser removido, pois esta ação é irreversível.

#### Request

```
curl --location --request DELETE '/feed/5b308629-cf63-433e-8334-666697f8cdd6' \
--header 'Authorization: Bearer user:5b308629-cf63-433e-8334-666697f8cdd6'
```

#### Response

```
// Não retorna nada
```