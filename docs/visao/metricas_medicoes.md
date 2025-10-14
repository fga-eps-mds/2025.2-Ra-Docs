# GQM de Medições

Para o nosso produto de software Aton, foram escolhidos dois objetivos comuns e essenciais: **qualidade do produto** (código) e **gerenciamento de processo** (andamento do projeto).

# **1**: Objetivo da Medição 1: Garantir a qualidade e manutenibilidade do código

- **Finalidade**: Analisar
- **Objeto**: Código-fonte do projeto
- **Com o propósito de**: avaliar
- **Foco**: a sua qualidade e manutenibilidade
- **Do ponto de vista**: da equipe de desenvolvimento
- **No contexto de**: Desenvolvimento de novas funcionalidades e correções de bugs no projeto.

## Questões e métricas associadas à Medição 1:

## **1.1**: A complexidade do código está sob controle, facilitando manutenção e refatoração futura?
### **1.1.1**: Métrica da complexidade ciclomática por função

- **Definição**: Mede o número de caminhos linearmente independentes através do código de uma função. Em termos simples, indica quão complexa (muitos `if`s, `else`s, `loops`) uma função é.

- **Forma de cálculo**: Será calculada automaticamente por ferramentas de análise estática de código (linters) no projeto, por meio de plugins do *ESLint*.

- **Escala de unidade**: Número inteiro.
- **Valores esperados**: Idealmente, a complexidade ciclomática deve ser **menor que $10$**. Valores acima de $15$ são um sinal de alerta para refatoração.
- **Forma de análise (5W, 1H)**: 
    - **O quê?**: Analisar relatórios de linter.
    - **Por quê?**: Para identificar funções que são difíceis de entender, testar e manter.
    - **Quem?**: O desenvolvedor responsável pela funcionalidade e o revisor do PR, juntamente com a equipe de QA.
    - **Quando?**: Durante o desenvolvimento e obrigatoriamente antes de aprovar um PR para a branch `develop`.
    - **Onde?**: No ambiente de desenvolvimento local.
    - **Como?**: Se uma função excede o limite, a equipe deve decidir se refatora imediatamente ou cria uma task de débito técnico.

## **1.2**: Estamos construindo uma interface consistente e evitando duplicação de código?
### **1.2.1**: Taxa de reutilização de componentes de UI
- **Definição**: Mede a proporção de elementos na interface que são construídos a partir de uma biblioteca de componentes compartilhada (ex: um `Button` customizado) em vez de estilos ou códigos específicos para cada tela.
- **Forma de cálculo**: Esta métrica é mais qualitativa. $\frac{\text{Número de componentes reutilizados na tela}}{\text{Número total de elementos de UI na tela}}$. Pode ser calculada durante a revisão de código (*Code Review*).
- **Escala de unidade**: Percentual ($\%$).
- **Valores esperados**: Não há um número fixo, mas a equipe deve buscar uma tendência crescente na reutilização. Sempre prefira criar/usar um componente genérico a criar um específico.
- **Forma de análise (5W, 1H)**:
    - **O quê?**: O design e o código de novas telas.
    - **Por quê?**: Para garantir consistência visual, reduzir o código duplicado e acelerar o desenvolvimento futuro.
    - **Quem?**: A equipe de Frontend, especialmente durante o planejamento de novas telas e na revisão de PR.
    - **Quando?**: Durante a revisão de código de qualquer nova funcionalidade de UI.
    - **Onde?**: No GitHub.
    - **Como?**: Se for identificada a criação de um componente que poderia ser genérico, o revisor deve solicitar a refatoração para a biblioteca compartilhada do time.

## **1.3**: Estamos entregando um código com um nível aceitável de defeitos?
### **1.3.1**: Densidade de defeitos (Defect Density)

- **Definição**: Relação entre o número de defeitos (bugs confirmados) encontrados e o tamanho do código, medido em milhares de linhas de código (KLOC, Kilo Lines of Code).
- **Forma de cálculo**: $\text{Densidade} =  \frac{\text{(Número total de  Bugs Válidos)}}{\text{Total de linhas de código} \div 1000}$
- **Escala de unidade**: Defeitos/KLOC.
- **Valores Esperados**: Um valor alvo seria menor que **$5$ Defeitos/KLOC** ao final do ciclo de entrega (Sprint).
- **Forma de análise (5W, 1H)**:
    - **O quê?**: Acompanhar a tendência de densidade de defeitos.
    - **Por quê?**: Para avaliar a eficácia do processo de testes e da qualidade geral das entregas. Um aumento súbito pode indicar problemas no processo.
    - **Quem?**: Equipe de QA.
    - **Quando?**: Ao final de cada sprint.
    - **Onde?**: Nas reuniões de retrospectiva da Sprint.
    - **Como?**: Comparar o valor atual com o histórico. Se a métrica estiver piorando, a equipe deve discutir possíveis causas (ex: testes insuficientes, pressa na entrega, complexidade do novo módulo).

# **2**: Objetivo da Medição 2: Acompanhar a eficiência do processo de desenvolvimento
- **Finalidade**: Analisar
- **Objeto**: o processo de desenvolvimento da equipe
- **Com o propósito de**: avaliar
- **Foco**: a previsibilidade e a eficiência das entregas
- **Do ponto de vista**: do Scrum Master
- **No contexto de**: planejamento e execução das Sprints de desenvolvimento.

## Questões e métricas associadas ao Objetivo 2:
## **2.1**: Nossa equipe consegue prever e entregar o trabalho planejado a cada ciclo?
### **2.1.1**: Débito Técnico (Tech Debt) da Sprint.
- **Definição**: Mede a quantidade de trabalho (representado pelas tasks) que a equipe consegue concluir (entregar) dentro de uma Sprint.
- **Forma de cálculo**: $\text{Total de tarefas concluídas} = \sum_{i=1}^{n} \text{tarefas}_i$, onde $i$ é cada item movido para "Concluído" ao fim da Sprint.
- **Escala de unidade**: Número de tarefas.
- **Valores esperados**: Não existe um valor esperado. A objetivo é que, após 2 ou 3 Sprints, o valor se torne **estável e previsível**. Uma grande variação entre Sprints sugere problemas de planejamento ou impedimentos.
- **Forma de análise (5W, 1H)**:
    - **O quê?**: A medição de velocidade/débito da equipe ao longo do tempo.
    - **Por quê?**: Para melhorar a capacidade de planejamento futuro (previsibilidade) e entender a capacidade real de trabalho da equipe.
    - **Quem?**: Toda a equipe, liderada pelo Scrum Master.
    - **Quando?**: Na reunião de retrospectiva da Sprint.
    - **Onde?**: No quadro Scrum da equipe.
    - **Como?**: Analisar por que o débito foi maior ou menor que o esperado (ex: a equipe foi superestimada, muitos impedimentos). A análise traz métricas para o planejamento da próxima Sprint.

# **3**: Objetivo da Medição 3: Garantir a performance e a boa Experiência de Usuário (UX)
- **Finalidade**: Analisar
- **Objeto**: a aplicação mobile (frontend)
- **Com o propósito de**: avaliar
- **Foco**: o desempenho de renderização e a fluidez da interação
- **Do ponto de vista**: do usuário final
- **No contexto de**: uso da aplicação em diferentes dispositivos e condições de rede.

## Questões e métricas associadas ao Objetivo 3:
## **3.1**: O aplicativo parece rápido e responsivo para o usuário?
### **3.1.1**: Tempo de carregamento da tela (Screen Load Time)
- **Definição**: Mede o tempo, em milissegundos, desde que o usuário realiza uma ação (ex: clica em um item) até que a tela seguinte esteja completamente carregada e interativa.
- **Forma de cálculo**: $\frac{\text{Tempo de interatividade}}{\text{Tempo do input de usuário}}$. O cálculo é feito por ferramentas de monitoramente de performance (APM). É possível medir com as ferramentas de desenvolvedor do React Native.
- **Escala de unidade**: Milissegundos ($\text{ms}$).
- **Valores esperados**: $ \lt 500\text{ms}$ para telas críticas (ex: feed de eventos, login), $ \lt 200\text{ms}$ para telas mais simples (ex: configurações).
- **Forma de análise (5W, 1H)**:
    - **O quê?**: Relatórios de performance das telas mais acessadas.
    - **Por quê?**: Para identificar gargalos que prejudicam a percepção de velocidade do app.
    - **Quem?**: Equipe de Frontend e Equipe de QA.
    - **Quando?**: Em ciclos de teste antes de uma nova versão.
    - **Onde?**: Na ferramenta de APM escolhida.
    - **Como?**: Telas lentas devem gerar tarefas de otimização (ex: otimizar imagens, reduzir chamadas de API, *lazy loading*).

## **3.2**: O aplicativo é estável e não fecha inesperadamente?
### **3.2.1**: Taxa de sessões livres de crash (Crash-Free Sessions Rate)
- **Definição**: Percentual de sessões de uso do aplicativo que são concluídas sem que ocorra nenhum crash (erro fatal que fecha o app).
- **Forma de cálculo**: $(1 - \frac{\text{Número de sessões com crash}}{\text{Número total de sessões}}) \times 100$. Com ferramentas como o *Sentry*,  esse dado é coletado automaticamente.
- **Escala de unidade**: Percentual ($\%$).
- **Valores esperados**: A meta deve ser $\geq 98\%$.
- **Forma de análise (5W, 1H)**:
    - **O quê?**: O painel de estabilidade da ferramenta utilizada (como *Sentry*), ou a medição da taxa das sessões ao longo do tempo.
    - **Por quê?**: Para garantir a confiabilidade do produto e a confiança do usuário.
    - **Quem?**: Equipe de Frontend e Equipe de QA.
    - **Quando?**: Continuamente, a cada ciclo de teste.
    - **Onde?**: No painel de estabilidade ou nas medições feitas.
    - **Como?**: Analisar os relatórios de crash para entender a causa (dispositivo, versão do sistema, ação do usuário) e priorizar a correção dos bugs mais impactantes.

# **4**: Objetivo da Medição 4: Avaliar a eficácia e abrangência dos testes automatizados
- **Finalidade**: Analisar
- **Objeto**: Suite de testes automatizados (unitários e de integração)
- **Com o propósito de**: avaliar
- **Foco**: a sua abrangência, eficácia na detecção de defeitos e o seu impacto na produtividade
- **Do ponto de vista**: da equipe de desenvolvimento e da equipe de QA
- **No contexto de**: garantir a estabilidade do software a cada nova alteração do código-fonte.

## Questões associadas ao Objetivo 4:
## **4.1**: Nossos testes unitários estão cobrindo uma parte significativa e crítica do nosso código-fonte?
### **4.1.1**: Cobertura de código por testes unitários (Code Coverage)
- **Definição**: Mede o percentual de linhas, branches (`if/else`), e funções do código-fonte que são executados pela suite de testes unitários.
- **Forma de cálculo**: É calculada automaticamente por ferramentas como o *Jest* durante a execução dos testes. O *Jest* gera relatórios de cobertura com o comando `npm test -- --coverage`.
- **Escala de Unidade**: Percentual ($\%$).
- **Valores esperados**:
    - **Backend**: $\gt 80\%$ para as camadas de serviço e lógica de negócio (as regras mais importantes).
    - **Frontend**: $\gt 70\%$ para componentes complexos e funções de manipulação de estado.
    - **Importante**: A qualidade dos testes é fundamental.
- **Forma de análise (5W, 1H)**:
    - **O quê?**: Relatório de cobertura gerado a cada execução de testes na pipeline de *CI/CD*.
    - **Por quê?**: Para identificar partes críticas do sistema que não estão testadas e que representam um risco a cada nova alteração.
    - **Quem?**: O desenvolvedor que submete o PR e orevisor.
    - **Quando?**: A cada PR. A pipeline pode ser configurada para falhar se a cobertura do novo código for inferior ao alvo definido.
    - **Onde?**: GitHub Actions.
    - **Como?**: Analisar quais arquivos ou funções tem baixa cobertura. Se forem partes críticas, deve-se criar tarefas para escrever mais testes unitários antes da aprovação do código.
## **4.2**: Nossos testes de integração estão sendo eficazes em encontrar problemas de comunicação entre componentes antes que o código seja mesclado?
### **4.2.1**: Taxa de sucesso das builds na branch de desenvolvimento (`develop`).
- **Definição**: Mede o percentual de builds na branch `develop` (ou `main`) que são concluídos com sucesso, passando por todas as etapas, incluindo a suíte de testes de integração.
- **Forma de cálculo**: $\frac{\text{Número de builds com sucesso}}{\text{Número total de builds na branch}} \times 100$. Esses dados são fornecidos pelo GitHub Actions.
- **Escala de unidade**: Percentual ($\%$).
- **Valores esperados**: Uma taxa $\gt 95\%$ é um sinal de uma branch saudável. Uma taxa muito baixa pode indicar que os testes de integração estão constantemente quebrando, o que é bom por pegar erros, mas pode também sinalizar testes instáveis ou problema de configuração no GitHub Actions.
- **Formas de Análise (5W, 1H)**:
    - **O quê?**: O histórico de execuções (workflows) da pipeline de CI/CD.
    - **Por quê?**: Para garantir que a principal branch de desenvolvimento esteja sempre em um estado estável e funcional.
    - **Quem?**: O líder técnico da equipe.
    - **Quando?**: Semanalmente, em uma revisão do processo de desenvolvimento.
    - **Onde?**: No GitHub Actions.
    - **Como?**: Investigar as falhas. Foram bugs reais que os testes pegaram? Foram problemas de ambiente ou testes instáveis? É preciso criar tarefas para corrigir a própria suíte de testes.

