## 2.4 Abstração e Implementação

### 2.4.1 Visão Lógica

Seguindo a estrutura de Cliente-Servidor em construção de software MonoRepo, temos a seguinte visão lógida do software:

> Estrutura Macro

O projeto é estuturado em monorepo, com gerenciamento feito pelo `PNPM` e possui otimização pelo Turborepo, com diferenciação entra FrontEnd e BackEnd.

A raíz do projeto possui o arquivo pnpm-workspace.yaml, que define:

- `apps/*` contendo as aplicações executáveis do aplicativo móvel e API
- `packages/*` contendo as configurações compartilhadas como Linters, Configs de testes e outros, usados pela aplicação em apps.

> Módulos Principais

A pasta `apps/` possui os componentes principais do sistema:

### a) O módulo `apps/api`

É o módulo de API do backend do Projeto, constituído em Node.js com TypeScript

- O Framework utiliza **Express.JS** e **ExpoRouter** para roteamento e gerenciamento de requisições.
- O **Banco de dados** utiliza o **Prisma** `(Prisma/schema.prisma)` para se comunicar com o banco de dados **Postgres.SQL**, rodando a partir de um container do **DOCKER**.
- A **estrutura de módulos**: A API é organizada por _features_ como visto em `src/modules/user` e `src/modules/auth` e cada módulo contém: `routes.ts` para definir endpoints da API, `controllers.ts` para orquestrar a lógica de requisição, `services.ts` contendo a lógica de negócios. `repository.ts` que abstrai o acesso direto ao banco de dados do **Prisma**.
- Os **testes de API** são configurados com o **JEST** e estáo na pasta `src/__tests__`, com foco em testes de integração e testes unitários.

### b) Módulos FrontEnd `apps/mobile`

É o aplicativo móvel principal, desenvolvido com **REACT NATIVE** e **EXPO**.

- **Roteamento**: É feito através do **ExpoRouter**, com uma abordagem _file based routing_ em que a estrutura de diretórios define a rota de navegaçõa do app.
- `(Auth)`: Define a tela de autenticação como: `login.tsc` e `cadastro.tsx`.
- `(Dashboard)`: Define a área logada do aplicativo, e possui páginas como (`Home.tsx` e `Teams.tsx`).
- **Componentização**: O sistema está organizado com uma pasta dedicada a **componentes reutilizáveis** em (`components/`).
- **Estilização e temas**: São definidos por designs (`constants/Colors.ts` e `constants/Fonts.ts` e `constants/Theme.ts`).
- Os **Testes**: são encontrados na pasta (`__tests__/`) e utilizam o **JEST** (com o React Native Testing Library) para testar componentes e páginas (`components/` e `paginas/`)

### C) Módulos Compartilhados (packages)

O diretório packages é fundamental para a estratégia do monorepo, garantindo que todas as apps sigam os mesmos padrões de qualidade de código:

- `packages/eslint-config`: Configuração base do ESLint, compartilhada entre api e mobile.

- `packages/jest-config`: Presets de configuração do Jest.

- `packages/prettier-config`: Regras de formatação de código (Prettier).

- `packages/typescript-config`: Arquivos tsconfig.json base, garantindo consistência nas configurações do TypeScript.

  > Diagrama UML:

![alt text](../assets/imgs/logica.jpg)

- Parte 2 Classes e persistência:
  > Criar e explicar o diagrama de classes em alto nível, mostrando persistência e lógica de negócio. Explicar interações entre camadas.

entrega: `Diagrama + texto`

## 2.4.2 Visão de Dados (MER)

O Modelo Entidade-Relacionamento (MER) do sistema foi desenvolvido para representar de forma clara e estruturada as entidades principais, seus atributos e os relacionamentos entre elas. Este modelo serve como base para a criação do esquema do banco de dados relacional, garantindo que os dados sejam armazenados de maneira eficiente e consistente.

![MER](../assets/imgs/MER.png){ width="700" }

<p align="center"><em>Figura 2.4.1 - Modelo Entidade-Relacionamento (MER) do Sistema</em></p>
<p align="center"><em>Fonte: <a href="https://github.com/CODEbugging3000">Gabriel Alves</a></em></p>

O MER inclui as seguintes entidades principais:

- **User (Usuário):** Representa os usuários do sistema, com atributos principais como ID, nome, userName, email e senha, ProfileType (tipo de perfil).
- **Group (Grupo):** Representa grupos de usuários, com atributos como ID, nome, bio, groupType (tipo de grupo que pode ser ATLÉTICA ou AMADOR).
- **Post (Publicação):** Representa as publicações feitas pelos usuários admins de grupos, contendo atributos como ID, title, content (conteúdo), date_of_post (data e hora da publicação), postType (tipo de publicação que pode ser EVENTO ou GERAL), location e event_date opcionais para eventos.
- **Comment (Comentário):** Representa os comentários feitos pelos usuários nas publicações, com atributos como ID, content (conteúdo) e createdAt (data e hora do comentário).
- **Match (Partida):** Representa partidas esportivas organizadas por grupos, com atributos como ID, home_team (time da casa), away_team (time visitante), match_date (data e hora da partida), location (local) e score_home e score_away para o placar.
- **Teams (Times):** Entidade fraca que representa os times associados as partidas, com apenas um atributo userId.
- **Report (Denúncia):** Representa denúncias feitas por usuários contra publicações ou comentários, com atributos como ID, reason (motivo) e createdAt (data e hora da denúncia).

Os relacionamentos entre as entidades são definidos para refletir as interações e dependências do sistema, como a associação entre usuários e grupos, publicações e comentários, e partidas e times. Foram identificados os tipos de relacionamentos (1:1, 1:N, N:M) e as cardinalidades para garantir a integridade dos dados.

Os relacionamentos principais incluem:

- Um **User** pode pertencer a muitos **Groups** (N:M), e um **Group** pode ter muitos **Users**.
- Um **User** pode seguir **(Follow)** muitos **Groups** (N:M), e um **Group** pode ser seguido por muitos **Users**.
- Um **User** pode criar muitas **Posts** (1:N), e cada **Post** é criado por um único **User** desde que seja um admin de um **Group**.
- Um **Post** pode ter muitos **Comments** (1:N), e cada **Comment** é associado a um único **Post**.
- Um **User** pode curtir **(postLike)** muitas **Posts** (N:M), e um **Post** pode ser curtido por muitos **Users**.
- Um **User** pode fazer vários **Comments** (1:N), e cada **Comment** é feito por um único **User**.
- Um **User** pode marcar presença **(attendance)** em muitos **Events** (N:M), e um **Event** pode ter muitos **Users** presentes.
- Um **User** pode denunciar **(Report)** muitas **Posts** e **Comments** (1:N), e cada **Report** é feito por um único **User**.
- Um **User** pode criar **(publish)** muitas **Matches** (1:N), e cada **Match** é criada por um único **User**.
- Um **User** pode se inscrever em muitas **Matches** (N:M), e uma **Match** pode ter muitos **Users** inscritos.
- Uma **Match** tem dois **Teams** (1:2), e cada **Team** está associada a uma única **Match**.

Os atributos de cada entidade foram cuidadosamente definidos para capturar todas as informações necessárias para o funcionamento do sistema, garantindo que o modelo atenda aos requisitos funcionais e não funcionais estabelecidos, sabendo que podem sofrer alterações futuras de acordo com a evolução do projeto.

## 2.5 Visão de Implantação

A visão de implantação deste projeto detalha a infraestrutura de software e as configurações de execução do sistema em um ambiente de desenvolvimento local (`localhost`).

O foco é garantir a **paridade de ambiente** entre os desenvolvedores, facilitando a colaboração e eliminando inconsistências de configuração.

---

### 2.5.1 Descrição da Infraestrutura de Implantação

O sistema é executado inteiramente em máquinas locais, por adotarmos a estratégia de construção com monorrepositório, e uma arquitetura cliente-servidor.

- **Cliente (Front-end):** A aplicação é desenvolvida em **React Native** . Ela é executada em um emulador de dispositivo móvel (Android Studio ou iOS Simulator) ou em um dispositivo físico conectado à máquina de desenvolvimento.
- **Servidor (Back-end):** Desenvolvido em **Node.js** com o framework **Express**. O Express é responsável por criar e gerenciar os endpoints da API RESTful.
- **Armazenamento de Dados:** O sistema utiliza **PostgreSQL** para armazenamento de dados.
- **Comunicação:** A aplicação React Native utiliza a biblioteca **Axios** para realizar as requisições HTTP (GET, POST, PUT, DELETE) aos _endpoints_ expostos pela API Express. Para que essa comunicação entre "origens" diferentes (o app e a API, rodando em portas distintas) seja permitida, o _middleware_ **CORS** (Cross-Origin Resource Sharing) está configurado no servidor Express.
- **Repositório:** O código-fonte de todos os componentes (front-end e back-end) é gerenciado em um **Monorepositório** no GitHub.

### 2.5.2 Justificativa das Tecnologias

A seleção de tecnologias foi baseada na simplicidade, produtividade e no objetivo de simular uma arquitetura de cliente-servidor em ambiente local.

- **Implantação Local (`localhost`):** por ser um projeto acadêmico, não se enxergou motivos para adoção de uma hospedagem pela complexidade e os custos da configuração de provedores de nuvem (AWS, Azure).
- **Docker:** Garante que todos os desenvolvedores e clientes executem instâncias idênticas do banco de dados PostgreSQL e dos serviços de back-end.
- **PostgreSQL:** Escolhido por ser um banco de dados robusto, de código aberto e amplamente utilizado no mercado.
- **React Native:** Selecionado por permitir o desenvolvimento de aplicações móveis para iOS e Android a partir de uma única base de código (codebase), otimizando o esforço de desenvolvimento.
- **Express (Node.js):** Sendo uma ferramenta leve, flexível e performática, sendo ideal para a construção de APIs RESTful de forma rápida e eficiente.
- **Monorepositório:** A abordagem de monorepo foi escolhida para facilitar a gestão do código, o compartilhamento de tipos e bibliotecas entre o front-end e o back-end, e simplificar a visualização do projeto como um todo, pelo fato de todo o projeto estar em um único repositório.

---

## 2.6 Restrições Adicionais

Esta aplicação foi projetada para atender restrições adicionais que garantem seu poder de negócio da aplicação, requisitos técnicos, qualidade e segurança dos dados de usuários.

### 2.6.1 Restrições Negociais

- O sistema destina-se à avaliação acadêmica, havendo planos para implantação em produção caso solicitado pela comunidade acadêmica.
- O projeto não deve gerar custos de infraestrutura, limitando-se a ferramentas gratuitas e de código aberto.

### 2.6.2 Restrições Técnicas (Pré-requisitos de Ambiente)

Para a correta execução e avaliação do projeto em um ambiente de desenvolvimento local, a máquina do usuário deve atender aos seguintes pré-requisitos de software:

- **Node.js:** É necessária a instalação do Node.js.
- **Gerenciador de Pacotes `pnpm`:** O projeto utiliza o `pnpm` para gerenciamento de dependências. Ele deve estar instalado e será o comando utilizado para instalar os pacotes.
- **Banco de Dados PostgreSQL:** Um servidor de banco de dados PostgreSQL deve estar em execução e acessível pela máquina local. (apesar de citar o docker como estratégia de containerização, uma instalação local do PostgreSQL também é compatível).
- **App Móvel (Para Testes Físicos):** Para executar o aplicativo em um dispositivo móvel físico (iOS ou Android), o usuário deve ter o aplicativo **Expo Go** instalado e estar conectado na mesma rede Wi-Fi que o computador que está executando o projeto.

### 2.6.3 Restrições de Qualidade

- **Disponibilidade:** O sistema deve estar funcional durante os períodos de desenvolvimento e para a apresentação final.
- **Testabilidade:** O foco dos testes unitários e automatizados deve ser a integração local entre o aplicativo React Native e os _endpoints_ da API, garantindo que os fluxos de dados ocorram corretamente.

### 2.6.4 Restrições de Segurança e Conformidade

Embora este seja um projeto acadêmico sem o uso de dados pessoais reais, o design do sistema busca seguir conceitualmente os princípios da **Lei Geral de Proteção de Dados (LGPD, Lei nº 13.709/2018)**.

O desenvolvimento da API (back-end) deve, como boa prática, mitigar os riscos de segurança mais críticos listados no **OWASP**, como _Injection_ (Injeção de Código) e _Broken Authentication_ (Autenticação Quebrada).
