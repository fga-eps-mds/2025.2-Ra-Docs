## API de Denúncias (Report)
Esta documentação detalha o endpoint responsável por registrar **denúncias de postagens ou comentários**. O sistema inclui uma lógica de moderação básica que remove automaticamente o conteúdo após atingir um limite de denúncias.

## Autenticação e Base URL

Todos os endpoints requerem **autenticação JWT obrigatória** para identificar o usuário que está denunciando (`reporterId`).

| Header | Valor |
| :--- | :--- |
| `Authorization` | `Bearer <token-jwt>` |

**Base URL:** `http://localhost:4000`

## Endpoint: Registrar Denúncia

Este endpoint permite que um usuário autenticado denuncie uma postagem ou um comentário.

### **POST** `/posts/:id/report`

**Observação:** A rota está atualmente definida como `/posts/:id/report`, mas o parâmetro `:id` é o ID do item a ser denunciado (Post ou Comentário), cujo tipo é especificado no corpo (`type`).

| Parâmetro | Local | Tipo de Dado | Descrição | Obrigatório |
| :--- | :--- | :--- | :--- | :--- |
| **`:id`** | Path | `string` (UUID) | Identificador único da **Postagem** ou **Comentário** a ser denunciado. | Sim |
| **`type`** | Body | `string` (Enum) | Tipo de conteúdo denunciado. Valores permitidos: **`"post"`** ou **`"comment"`**. | Sim |
| **`reporterId`** | Body | `string` (UUID) | O ID do usuário que está fazendo a denúncia. *Atenção: Idealmente, deveria ser extraído do JWT para segurança.* | Sim |
| **`reason`** | Body | `string` | O motivo da denúncia. Mínimo de **10 caracteres**. | Sim |


### Regras de Negócio e Moderação Automática

| Regra | Detalhe |
| :--- | :--- |
| **Registro** | Cria um novo registro na tabela `Report`, associando-o ao `postId` ou `commentId` e ao `reporterId`. |
| **Limite de Denúncias** | O limite de denúncias para acionar a ação de moderação é **10**. |
| **Ação para Postagens** | Se um **Post** atingir 10 denúncias: 1. O título e conteúdo são alterados para **`[REMOVIDO PELO SISTEMA]`**. 2. **Todas as denúncias** associadas àquela postagem são removidas. |
| **Ação para Comentários** | Se um **Comentário** atingir 10 denúncias: 1. O **Comentário** é **deletado**. 2. **Todas as denúncias** associadas àquele comentário são removidas. |


### Respostas da API (Status Codes)

| Status Code | Cenário | Detalhe da Resposta |
| :--- | :--- | :--- |
| **201 Created** | Denúncia registrada com sucesso. Retorna o objeto da denúncia criada. |
| **400 Bad Request** | Campos obrigatórios ausentes ou inválidos (validação falhou). |
| **401 Unauthorized** | Token JWT ausente ou inválido. |
| **500 Internal Error** | Erro interno do servidor. |


## Exemplo de Uso (Denunciar Postagem)

#### Request

\`\`\`bash
# Denunciando o post com ID '8d9a7d2b-1a2b-3c4d-5e6f-7a8b9c0d1e2f'
curl --location --request POST 'http://localhost:4000/posts/8d9a7d2b-1a2b-3c4d-5e6f-7a8b9c0d1e2f/report' \
--header 'Authorization: Bearer <token-jwt>' \
--header 'Content-Type: application/json' \
--data-raw '{
    "reporterId": "bca5f2f2-1a4b-44c7-b31a-4b13d5e0d7ef",
    "reason": "Conteúdo de ódio e linguagem inadequada.",
    "type": "post"
}'
\`\`\`

#### Response (201 Created)

\`\`\`json
{
    "id": "e4f3c2b1-9a8b-7c6d-5e4f-3a2b1c0d9e8f",
    "postId": "8d9a7d2b-1a2b-3c4d-5e6f-7a8b9c0d1e2f",
    "reporterId": "bca5f2f2-1a4b-44c7-b31a-4b13d5e0d7ef",
    "reason": "Conteúdo de ódio e linguagem inadequada.",
    "commentId": null,
    "createdAt": "2025-11-12T02:30:00.000Z"
}
\`\`\`


## Estrutura de Código

| Arquivo | Função Principal |
| :--- | :--- |
| **`report.routes.ts`** | Define a rota `/posts/:id/report` e aplica os middlewares (`auth`, `validateRequest`, `catchAsync`). |
| **`report.validation.ts`** | Usa **Zod** para validar os parâmetros (`id`) e o corpo (`reporterId`, `reason`, `type`). |
| **`report.controller.ts`** | Recebe a requisição, valida a presença de campos obrigatórios e chama o serviço. |
| **`report.service.ts`** | Contém a **Regra de Negócio**: cria o registro, verifica a contagem de denúncias (`>= 10`) e aplica a lógica de moderação automática (atualizar Post ou deletar Comentário). |
| **`report.repository.ts`** | Abstrai as interações com o **Prisma** (CRUD de Denúncias, Contagem, Atualização de Post e Deleção de Comentário/Denúncias). |