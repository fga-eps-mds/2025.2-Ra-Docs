# 6. TESTES DE SOFTWARE

Nesta seção, evidenciamos como foram criadas estratégias para aplicação dos testes desenvolvidos, realizados e validados pelo grupo durante a implementação de funcionalidades, além de um roteiro de testes que especifica as alterações e demonstra a funcionalidade do projeto. Através desta metodologia, garantimos a qualidade, responsividade e eficiência do produto final mediante as exigências de nossos clientes.

## 6.1 Estratégia de testes

A estratégia de testes do projeto Aton visa garantir que as funcionalidades do sistema estejam corretas, seguras e em conformidade com os requisitos definidos. Para este projeto será adotada uma estratégia de testes de que comtempla múltiplos níveis, tipos, ambientes e formas de análise, garantindo a qualidade e confiabilidade do aplicativo. 

### 6.1.1 Níveis de testes abordados

- **Testes unitários:** Validação de funções e componentes isolados, como cadastro de usuários, criação de eventos e autenticação. 

- **Testes de Integração:** Verificação da comunicação entre módulos, como o fluxo de criação de um evento sendo automaticamente exibido no feed de seguidores.

- **Testes de Sistema:** Avaliação do aplicativo de forma completa, considerando o comportamento esperado para operações típicas do usuário, como confirmar presença em eventos e comentar.

### 6.1.2 Tipos de Testes Abordados 

- **Testes Funcionais:** Validam o comportamento esperado do sistema.

- **Testes Não Funcionais:**
    - **Usabilidade:** Facilidade de navegação na interface. 

    - **Desempenho:** Tempo de resposta na listagem de eventos e notificações. 

    - **Segurança:** Verificação de proteção de dados de usuários e permissões de acesso. 

    - **Negativo:** Testa situações inesperadas de entrada com tipos inválidos inseridos intencionalmente.

### 6.1.3 Ambientes de Testes do projeto
    
A política de branches e commits registrada em [PROCESSO DE DESENVOLVIMENTO DE SOFTWARE](processo_desenv.md) será adotada para manter a qualidade e rastreabilidade dos testes em branches separadas e identificadas.

- **Ambiente de desenvolvimento:** Os desenvolvedores usam esse ambiente para escrever, depurar e testar código durante os estágios iniciais da criação do software. Ele incluirá ambientes de desenvolvimento integrado (IDEs), sistema de controle de versão (Git) e ferramentas de depuração de código, focando em testes unitários.

- **Controle de acesso:** Os mecanismos de controle de acesso garantem que somente pessoal autorizado possa interagir com o ambiente de teste. Isso inclui o usuário autenticação, acesso baseado em função, e conexões seguras para evitar alterações não autorizadas ou violação de dados.

- **Controle de qualidade/teste:** Dedicado à garantia de qualidade (QA) equipes, este ambiente é usado para executar testes funcionais, de regressão e de integração. Ele espelha o ambiente de produção o mais próximo possível, garantindo que todos os componentes funcionem perfeitamente juntos. O objetivo é identificar e corrigir problemas que podem afetar a funcionalidade ou experiência do usuário. 

O ambiente de testes utilizará containers Docker para simular produção, garantindo compatibilidade e previsibilidade dos resultados.

### 6.1.4 Formas de Análise dos Testes 

- Comparação entre resultado previsto X realizado. 

- Registro de logs de execução para cada caso de teste. 

- Defeitos identificados serão registrados no sistema de issues do GitHub do projeto.

### 6.1.5 Resultados Obtidos 

- Se resultado previsto = realizado → teste aprovado. 

- Se resultado previsto ≠ realizado → teste reprovado. O defeito será registrado, corrigido e o teste reexecutado até aprovação. 

## 6.2 Roteiro de teste:

O roteiro de testes constitui o planejamento detalhado dos casos de teste a serem executados. Ele será representado em formato tabular, contendo: 

- Código de identificação do teste;

- Nome do teste;

- Objetivo do teste;

- Nível do teste (unitário, integrado, sistema);

- Tipo de teste (funcional, não funcional. Se não funcional tipo: usabilidade, portabilidade, etc);

- Precondições para o teste ser realizado;

- Definição de Aceito rejeitado dos testes propostos (resultados esperados para o teste ser aceito como OK);

- Espaço para registro dos resultados do teste (com evidências objetivas);

- Reparos executados;

- Quantidade de ciclos de testes executados para cada caso de teste proposto.