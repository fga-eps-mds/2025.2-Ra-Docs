## 2.4 Abstração e Implementação

### 2.4.1 Visão Lógica

- Parte 1 Módulos e pacotes:
  > Listar módulos do sistema e criar o diagrama de pacotes UML mostrando a organização da aplicação (camadas, comunicação).

entrega: `Diagrama + explicação`

- Parte 2 Classes e persistência:
  > Criar e explicar o diagrama de classes em alto nível, mostrando persistência e lógica de negócio. Explicar interações entre camadas.

entrega: `Diagrama + texto`

## 2.4.2 Visão de Dados (MER)

O Modelo Entidade-Relacionamento (MER) do sistema foi desenvolvido para representar de forma clara e estruturada as entidades principais, seus atributos e os relacionamentos entre elas. Este modelo serve como base para a criação do esquema do banco de dados relacional, garantindo que os dados sejam armazenados de maneira eficiente e consistente.

![MER](../assets/imgs/MER.png){ width="700" }
<p align="center"><em>Figura 2.4.1 - Modelo Entidade-Relacionamento (MER) do Sistema</em></p>
<p align="center"><em>Fonte: <a href="https://github.com/CODEbugging3000">Gabriel Alves</a></em></p>



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
