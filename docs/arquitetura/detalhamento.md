## 2.2 Detalhamento da Arquitetura

### 2.2.1 Detalhamento
A plataforma Aton é estruturada em uma arquitetura de **Cliente-Servidor**. Esta estrutura separa as responsabilidades em duas aplicações distintas: um **aplicativo móvel** (o cliente) e uma **API central** (o servidor). O código-fonte de ambas as aplicações é gerenciado de forma coesa dentro de um monorepo, utilizando *Turborepo e pnpm* para otimizar o desenvolvimento e as dependências. 

O **Cliente** da plataforma é o **aplicativo móvel**, desenvolvido em *React Native* com o ecossistema *Expo*. Esta camada é exclusivamente responsável pela **apresentação e pela interação com o usuário**. Sua função é **renderizar** a interface, **capturar entradas** (como formulários de login ou cadastro) e **formatar essas informações** para envio. O Front-end não contém lógica de negócio; ele funciona como um consumidor que **apenas solicita e exibe dados**, comunicando-se com o servidor através de requisições **HTTP (API REST)**.

O **Servidor**, que atua como o **cérebro central** da arquitetura, é uma **API RESTful** construída em *Node.js e Express*. Esta camada de aplicação é onde reside toda a **lógica de negócio** do Aton. Ela é responsável por receber as **requisições HTTP** do cliente móvel, **processar** as operações (como autenticar um usuário, criar um campeonato ou buscar notícias) e **orquestrar** o acesso aos dados.

A comunicação entre o cliente e os serviços de negócio do Back-end é protegida por uma camada de ***Middlewares*** do *Express*. Esta camada funciona como um "guardião" que **intercepta cada requisição**. Suas responsabilidades são cruciais para a segurança e integridade dos dados: ela realiza a **autenticação** (verificando tokens JWT para proteger rotas) e a **validação** (garantindo que os dados enviados, como um e-mail ou senha, estejam no formato correto), antes que a requisição seja processada pela **lógica de negócio principal**.

No nível mais fundamental da arquitetura está a **camada de persistência**, composta por um banco de dados relacional *PostgreSQL*. Este banco armazena todos os dados (usuários, partidas, postagens) da plataforma Aton em diferentes tabelas. O **Back-end** (especificamente, sua camada de repositório) é o único componente com permissão para se **comunicar** com o banco de dados. Essa separação garante que o acesso aos dados seja sempre mediado pela lógica de negócio e pelas regras de segurança da API.

![Diagrama esquemático geral da aplicação](../assets/imgs/diagrama-geral.png)

- Parte 2 Instanciação dos componentes:
> Instanciar os elementos da arquitetura (ex.: front-end, API, banco, serviços). Explicar como se relacionam e os protocolos utilizados.

entrega: `Texto + complemento ao diagrama anterior`