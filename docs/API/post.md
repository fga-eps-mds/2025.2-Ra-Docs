# Endpoint de Posts/eventos API
Esta coleção contém endpoints para gerenciar posts em um sistema de Feed de eventos e informações por parte publisher. Todos os endpoints requerem autenticação JWT via header `Authorization: Bearer` .

**Base URL:** `http://localhost:4000`

## Guia de Uso Geral

- Utilize os endpoints para criar, atualizar e deletar postagens, juntamente com o endpoint para deletar comentarios dentro de post criados pelo usuario.
- Sempre envie o token JWT no header Authorization.
- Consulte os exemplos de uso em cada endpoint para facilitar a integração.

## Endpoints disponíveis

- **Create_post:** Cria um novo post.
- **Update_post:** Atualiza as informações de um post já criado.
- **Delete_post:** Deleta um post.
- **Delete_comment:** Deleta um comentario dentro de um post criado pelo usuario.

Consulte a documentação detalhada em cada endpoint para informações de parâmetros, respostas e exemplos de uso.

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