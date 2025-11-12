# Módulo de Attendance (Presença/Participação)
Este módulo é responsável por gerenciar a **presença** ou **participação** de um usuário em uma **publicação** específica. Ele implementa uma lógica de "toggle" (alternar), onde uma nova participação é criada se não existir, ou uma existente é removida, ajustando o contador na publicação correspondente.

## Estrutura de Arquivos

* `attendance.controller.ts`: Lida com as requisições HTTP, validação básica e chama o serviço.
* `attendance.service.ts`: Contém a lógica de negócios para alternar a participação.
* `attendance.repository.ts`: Interage diretamente com o banco de dados (Prisma) para operações CRUD e atualização do contador da publicação.
* `attendance.routes.ts`: Define a rota HTTP para a funcionalidade de participação.
* `attendance.validation.ts`: Define o esquema de validação usando `zod`.

## `attendance.controller.ts`
Controlador que expõe a funcionalidade de alternar a participação através de uma rota HTTP.

### Classe `AttendanceController`

| Método | Descrição |
| :--- | :--- |
| `toggleAttendance` | Alterna o status de participação de um usuário em um post. |

#### `toggleAttendance(req: Request, res: Response): Promise<void>`

**Funcionalidade:**
Recebe o `postId` dos parâmetros da rota e o `authorId` do corpo da requisição para alternar a participação.

**Validação:**
Verifica se `postId` e `authorId` estão presentes. Se não, retorna um erro **400 Bad Request**.

**Resposta de Sucesso:**
Retorna o objeto `Attendance` criado ou deletado com status **201 Created**.

**Requisição Exemplo:**
```http
POST /posts/:postId/attendance
Content-Type: application/json
Authorization: Bearer <token>

{
  "authorId": "a1b2c3d4-e5f6-7890-1234-567890abcdef"
}