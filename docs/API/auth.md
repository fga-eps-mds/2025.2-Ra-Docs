# Autenticação e Autorização
Esta sessão aborda os conceitos de autenticação e autorização da API, incluindo métodos de autenticação, gerenciamento de tokens e práticas recomendadas para proteger recursos.

-----

## Módulo: Autenticação (Login)

Este módulo gerencia a autenticação e o login de usuários.

### 1\. Login de Usuário

Autentica um usuário com e-mail e senha. Em caso de sucesso, retorna um token JWT para ser usado em rotas protegidas, junto com os dados do usuário e quaisquer avisos (como a necessidade de definir um perfil).

`POST localhost:4000/login`

-----

### Request Body

O corpo da requisição deve ser um JSON contendo o e-mail e a senha do usuário.

**Exemplo:**

```json
{
  "email": "usuario@exemplo.com",
  "password": "minhasenha123"
}
```

**Campos:**

| Campo | Tipo | Obrigatório | Descrição |
| :--- | :--- | :--- | :--- |
| `email` | string | Sim | O e-mail de cadastro do usuário. Deve ter um formato válido. |
| `password` | string | Sim | A senha do usuário. (Mínimo 1 caractere). |

-----

### Success Response (`200 OK`)

Retorna um objeto com três chaves:

1.  `token`: O token de acesso JWT.
2.  `user`: Um objeto com as informações básicas do usuário.
3.  `warns`: Um array de strings contendo avisos. Este array **incluirá `"Configuração de perfil pendente."`** se o `profileType` do usuário for `null`, sinalizando ao frontend que o usuário deve ser redirecionado para a tela de escolha de perfil.

**Exemplo (Usuário com perfil pendente):**

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNseGFiY2RlMTIzIiwiZXhwIjoxNjk4NDI4MjIyfQ.exemploToken",
  "user": {
    "id": "clxabcde123",
    "name": "Nome do Usuário",
    "email": "usuario@exemplo.com"
  },
  "warns": [
    "Configuração de perfil pendente."
  ]
}
```

**Exemplo (Usuário com perfil já definido):**

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNseGFiY2RlMTIzIiwiZXhwIjoxNjk4NDI4MjIyfQ.exemploToken",
  "user": {
    "id": "clxabcde123",
    "name": "Nome do Usuário",
    "email": "usuario@exemplo.com"
  },
  "warns": []
}
```

-----

### Error Responses

O endpoint trata os erros e retorna mensagens claras.

| Código | Status | Motivo | Exemplo de Resposta (JSON) |
| :--- | :--- | :--- | :--- |
| `400` | Bad Request | Erro de validação do Zod (ex: e-mail inválido, senha não enviada). | `{"error": "Erro de validação", "issues": { ... }}` |
| `401` | Unauthorized | O e-mail fornecido não foi encontrado no banco de dados. | `{"error": "E-mail não encontrado."}` |
| `401` | Unauthorized | A senha fornecida não corresponde ao hash no banco de dados. | `{"error": "E-mail ou senha incorretos."}` |
| `500` | Internal Server | Erro raro (ex: o usuário existe, mas não possui `passwordHash`). | `{"error": "Conta de usuário inválida."}` |
