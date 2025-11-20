# Endpoint de PostLike API
Esta coleção contém os endpoints responsáveis por gerenciar as
**curtidas em postagens (posts/eventos)** dentro do sistema de Feed de
eventos e informações.\
Todos os endpoints requerem **autenticação JWT** via header
`Authorization: Bearer`.

**Base URL:** `http://localhost:4000`

## Guia de Uso Geral

-   Utilize os endpoints para **curtir ou descurtir** postagens do feed.
-   Sempre envie o token JWT no header Authorization.
-   Cada curtida é associada ao `authorId` (usuário autenticado) e ao
    `postId` (postagem alvo).
-   A lógica impede curtidas duplicadas de um mesmo usuário no mesmo
    post.
-   O contador de curtidas (`likesCount`) da postagem é atualizado
    automaticamente.

## Endpoints disponíveis

-   **Toggle_like:** Adiciona ou remove uma curtida em um post (funciona
    como "curtir/descurtir").

## POST Toggle_like

    POST http://localhost:4000/posts/:postId/like

**Descrição:**\
Adiciona ou remove a curtida de um usuário autenticado em uma postagem.\
Se o usuário ainda **não curtiu** o post, o endpoint **adiciona a
curtida**.\
Se o usuário **já curtiu**, o endpoint **remove a curtida existente**.

### Parâmetros obrigatórios

**Path Params:** - `postId` (string, UUID): Identificador único da
postagem a ser curtida.

**Body Params:** - `authorId` (string, UUID): Identificador único do
usuário que realiza a ação.

### Regras de Negócio

-   Caso o `postId` ou `authorId` não sejam informados, o sistema
    retorna **400 Bad Request**.
-   O endpoint atualiza o campo `likesCount` da tabela `Post`,
    incrementando ou decrementando conforme a ação.
-   A lógica impede curtidas duplicadas do mesmo usuário no mesmo post.

### Respostas esperadas

-   `201 Created`: Curtida adicionada ou removida com sucesso (retorna o
    registro da curtida ou a exclusão).\
-   `400 Bad Request`: Campos obrigatórios ausentes ou inválidos.\
-   `401 Unauthorized`: Token JWT ausente ou inválido.\
-   `500 Internal Server Error`: Erro interno ao processar a
    solicitação.

### Exemplo de uso

#### Request (Adicionar curtida)

``` bash
curl --location --request POST 'http://localhost:4000/posts/5b308629-cf63-433e-8334-666697f8cdd6/like' --header 'Authorization: Bearer user:5b308629-cf63-433e-833e-666697f8cdd6' --header 'Content-Type: application/json' --data-raw '{
    "authorId": "bca5f2f2-1a4b-44c7-b31a-4b13d5e0d7ef"
}'
```

#### Response (Curtida adicionada)

``` json
{
  "id": "d12f7a9e-4b7d-4a66-91cf-1b9ef3f4211f",
  "postId": "5b308629-cf63-433e-833e-666697f8cdd6",
  "userId": "bca5f2f2-1a4b-44c7-b31a-4b13d5e0d7ef",
  "createdAt": "2025-11-11T22:40:00.000Z"
}
```
#### Request (Remover curtida)

``` bash
curl --location --request POST 'http://localhost:4000/posts/5b308629-cf63-433e-833e-666697f8cdd6/like' --header 'Authorization: Bearer user:5b308629-cf63-433e-833e-666697f8cdd6' --header 'Content-Type: application/json' --data-raw '{
    "authorId": "bca5f2f2-1a4b-44c7-b31a-4b13d5e0d7ef"
}'
```

#### Response (Curtida removida)

``` json
{
  "id": "d12f7a9e-4b7d-4a66-91cf-1b9ef3f4211f",
  "postId": "5b308629-cf63-433e-833e-666697f8cdd6",
  "userId": "bca5f2f2-1a4b-44c7-b31a-4b13d5e0d7ef",
  "createdAt": "2025-11-11T22:40:00.000Z"
}
```

### Guia de uso

-   Use este endpoint para permitir que o usuário interaja com postagens
    através de curtidas.\
-   A mesma lógica será reaproveitada para o botão **"Eu vou"** em
    eventos futuros, permitindo marcar e desmarcar presença com o mesmo
    padrão de alternância (`toggle`).

### Middleware utilizado

-   `auth`: garante que o usuário esteja autenticado.
-   `validateRequest(PostLikeParamsSchema)`: valida o corpo e parâmetros
    da requisição.
-   `catchAsync`: encapsula funções assíncronas para tratamento de
    erros.

## Exemplo de fluxo completo

1.  Usuário faz login e obtém o token JWT.\
2.  Usuário envia uma requisição `POST /posts/:postId/like` com
    `authorId` no corpo.\
3.  O sistema:
    -   Verifica se o usuário já curtiu o post.
    -   Se **sim**, remove a curtida e decrementa o contador.
    -   Se **não**, adiciona a curtida e incrementa o contador.
4.  O endpoint retorna o objeto da curtida (adicionada ou removida).
