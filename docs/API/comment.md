## API de Comentários (Comment)
Esta documentação detalha os endpoints responsáveis pela gestão de **comentários** em postagens. O recurso permite listar, criar, visualizar, atualizar e deletar comentários associados a um `postId`.

## Autenticação e Base URL

Todos os endpoints requerem **autenticação JWT obrigatória** enviada no *header*.

| Header | Valor |
| :--- | :--- |
| `Authorization` | `Bearer <token-jwt>` |

**Base URL:** `http://localhost:4000`

## Endpoints Disponíveis

Todos os endpoints são aninhados sob o contexto de uma postagem, utilizando o parâmetro `postId` no caminho.

| Método | Rota | Descrição | Requer JWT |
| :--- | :--- | :--- | :--- |
| **GET** | `/posts/:postId/comments` | Lista todos os comentários no sistema (comportamento atual) ou em um post específico (esperado). | Sim |
| **POST** | `/posts/:postId/comments` | Cria um novo comentário na postagem especificada. | Sim |
| **GET** | `/posts/:postId/comments/:id` | Busca um comentário específico pelo seu ID. | Sim |
| **PATCH** | `/posts/:postId/comments/:id` | Atualiza o conteúdo de um comentário específico. | Sim |
| **DELETE** | `/posts/:postId/comments/:id` | Deleta um comentário específico. | Sim |

## POST /posts/:postId/comments (Criar Comentário)

Cria um novo comentário associado a uma postagem.

### Parâmetros

| Local | Parâmetro | Tipo de Dado | Descrição |
| :--- | :--- | :--- | :--- |
| **Path** | `:postId` | `string` (UUID) | **Obrigatório**. ID da postagem onde o comentário será criado. |
| **Body** | `authorId` | `string` (UUID) | **Obrigatório**. ID do usuário que está criando o comentário. *(Recomendação: Extrair do JWT por segurança).* |
| **Body** | `content` | `string` | **Obrigatório**. Conteúdo do comentário (mínimo 2, máximo 1000 caracteres). |

### Regras de Negócio

* O campo `commentsCount` da tabela `Post` é **incrementado** automaticamente após a criação bem-sucedida do comentário.
* A validação do corpo é feita via `Zod` (`createCommentSchema`).

### Respostas

| Status Code | Cenário | Retorno |
| :--- | :--- | :--- |
| **201 Created** | Comentário criado com sucesso. | Objeto do comentário criado. |
| **400 Bad Request** | `postId` ausente ou validação do corpo falhou. | \`{"message": "..."}\` |
| **401 Unauthorized** | Token JWT inválido. | |
| **500 Internal Error** | Erro interno do servidor. | |

## PATCH /posts/:postId/comments/:id (Atualizar Comentário)

Atualiza o campo `content` de um comentário existente.

### Parâmetros

| Local | Parâmetro | Tipo de Dado | Descrição |
| :--- | :--- | :--- | :--- |
| **Path** | `:id` | `string` (UUID) | **Obrigatório**. ID do comentário a ser atualizado. |
| **Body** | `content` | `string` | **Obrigatório**. Novo conteúdo do comentário (mínimo 10, máximo 1000 caracteres). |

### Regras de Negócio

* O *refine* do `updateCommentSchema` garante que o campo `content` esteja presente no corpo para a atualização.
* Assume-se que a lógica de permissão (se o `authorId` no JWT é o dono do comentário) é tratada no serviço ou middleware.

### Respostas

| Status Code | Cenário | Retorno |
| :--- | :--- | :--- |
| **200 OK** | Comentário atualizado com sucesso. | Objeto do comentário atualizado. |
| **400 Bad Request** | Falha na validação (`content` ausente ou inválido). | |
| **401 Unauthorized** | Token JWT inválido. | |
| **404 Not Found** | Comentário não encontrado. | |

## DELETE /posts/:postId/comments/:id (Deletar Comentário)

Deleta um comentário específico.

### Parâmetros

| Local | Parâmetro | Tipo de Dado | Descrição |
| :--- | :--- | :--- | :--- |
| **Path** | `:id` | `string` (UUID) | **Obrigatório**. ID do comentário a ser deletado. |

### Regras de Negócio

* A exclusão do comentário deve, idealmente, **decrementar** o `commentsCount` da `Post` associada (lógica não explícita no `repository.delete`).
* A validação usa `deleteCommentSchema`.

### Respostas

| Status Code | Cenário | Retorno |
| :--- | :--- | :--- |
| **204 No Content** | Comentário deletado com sucesso. | (Vazio) |
| **401 Unauthorized** | Token JWT inválido. | |
| **404 Not Found** | Comentário não encontrado. | |

## GET /posts/:postId/comments e GET /posts/:postId/comments/:id

### Listar Comentários

* **Rota:** `GET /posts/:postId/comments`
* **Comportamento:** Retorna todos os comentários do sistema.
* **Respostas:** **200 OK** (Lista de comentários) ou **204 No Content** (Nenhum comentário).

### Buscar por ID

* **Rota:** `GET /posts/:postId/comments/:id`
* **Comportamento:** Retorna um comentário específico.
* **Respostas:** **200 OK** (Objeto do comentário) ou **404 Not Found** (Comentário não encontrado).

## Estrutura de Código

| Arquivo | Função Principal |
| :--- | :--- |
| **`comment.routes.ts`** | Define todas as rotas CRUD e aplica middlewares (`auth`, `validateRequest`, `catchAsync`). |
| **`comment.validation.ts`** | Define os schemas **Zod** para validação de criação, atualização e IDs de comentários. |
| **`comment.controller.ts`** | Recebe a requisição, extrai parâmetros/corpo e chama o serviço para realizar as operações. |
| **`comment.service.ts`** | Contém a lógica de negócio (CRUD) e orquestra as chamadas ao repositório. |
| **`comment.repository.ts`** | Abstrai as interações com o **Prisma**, incluindo: `findMany`, `findById`, `create` (com incremento de `commentsCount` no Post) e operações de modificação. |