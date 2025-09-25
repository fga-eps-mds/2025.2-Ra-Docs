---
name: "\U0001F4DD Plano de Implementação da User Story"
about: Template para quebrar uma US em tarefas técnicas e planejar a implementação.
title: "[IMPL]: "
labels: ''
assignees: ''

---

### 🔗 User Story Associada
**Resolve:** #

### 📄 Resumo Técnico da Solução
---

### ✅ Checklist de Implementação

#### 🐘 **Backend (TypeScript)**
- [ ] **Modelagem e Migração:** Alterar/criar tabelas no banco de dados.
- [ ] **Endpoint:** Criar ou alterar o(s) endpoint(s) da API.
- [ ] **Lógica de Negócio:** Implementar as regras e serviços necessários.
- [ ] **Testes:** Escrever os testes unitários e/ou de integração.

#### ⚛️ **Frontend (React Native)**
- [ ] **Telas e Componentes:** Desenvolver as telas e componentes visuais.
- [ ] **Gerenciamento de Estado:** Controlar o estado da tela/global (loading, error, data).
- [ ] **Conexão com API:** Chamar o(s) endpoint(s) do backend e tratar a resposta.
- [ ] **Testes:** Escrever testes para os componentes ou fluxos.

#### ⚙️ **Outras Tarefas**
- [ ] **Variáveis de Ambiente:** Adicionar/atualizar `env vars`, se necessário.
- [ ] **Revisão de Pares (PR):** Realizar o code review do backend e do frontend.

---

### 🏁 Definição de "Pronto" (Definition of Done)
- [ ] O código foi revisado e aprovado (PR merged).
- [ ] Todos os itens do checklist de implementação foram concluídos.
- [ ] A funcionalidade passou nos testes automatizados.
- [ ] A funcionalidade foi validada e atende aos Critérios de Aceite da US.
