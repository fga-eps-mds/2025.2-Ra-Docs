## 2.3 Arquitetura x Produto

### 2.3.1 Metas e restrições arquiteturais

Assegurando que o produto final atenda aos requisitos não funcionais de desempenho, disponibilidade, segurança e padronização de código, as metas e restrições arquiteturiais do **Aton** garantem qualidade organizacional para o desenvolvimento da nossa plataforma.

As metas e restrições arquiteturais descritas a seguir definem princípios essenciais de cada tópico, assegurando que o sistema mantenha estabilidade e coerência técnica ao longo do desenvolvimento.


| **Categoria** | **Meta** | **Justificativa** |
|----------------|-----------|-------------------|
| **Desempenho** | O tempo médio de resposta da **API REST** deve ser inferior a **500 ms** para operações simples e até **2 s** para consultas mais complexas (como listagem de eventos). | Garante uma experiência responsiva no aplicativo móvel, evitando travamentos e lentidão perceptível para o usuário final. |
| **Desempenho** | O servidor deve ser capaz de lidar com **100 requisições simultâneas** sem degradação significativa. | Mantém a estabilidade da API mesmo durante picos de acesso, como eventos ou atualizações em tempo real. |
| **Disponibilidade** | O sistema deve atingir **99% de disponibilidade**, especialmente nos módulos de autenticação e feed. | A confiabilidade é essencial para garantir o acesso contínuo de usuários ao aplicativo, evitando falhas críticas. |
| **Segurança** | Toda comunicação entre cliente e servidor deve ocorrer via **HTTPS**. | Garante a confidencialidade dos dados trafegados entre o aplicativo e a API. |
| **Segurança** | As senhas devem ser armazenadas com **hash** utilizando *bcrypt*. | Protege credenciais de usuários contra acesso indevido em caso de vazamento de dados. |
| **Segurança** | O acesso às rotas privadas deve ser controlado por **autenticação JWT (JSON Web Token)**. | Assegura que apenas usuários autenticados possam acessar recursos sensíveis. |
| **Segurança** | Implementação de **middlewares** no *Express* para validação e sanitização de entradas. | Evita injeções e falhas de segurança decorrentes de dados malformados. |
| **Padrões de Codificação** | O código deve seguir as convenções do **ESLint** e **Prettier** definidas no monorepo. | Mantém a consistência entre os repositórios e facilita a colaboração entre desenvolvedores. |
| **Padrões de Codificação** | A arquitetura deve manter a separação entre **rotas**, **controladores**, **serviços** e **repositórios**. | Ajuda a manter o sistema bem estruturado e simples de modificar. |


 Esses critérios orientam tanto o desenvolvimento do **cliente móvel** quanto da **API central**, garantindo padrões e qualidade para os usuários da aplicação.


### 2.3.2 Backlog do Produto (escopo do produto)
- **Funcionamento:**
O aplicativo Aton tem como objetivo **promover** a **saúde** e o **lazer** entre os **estudantes da Universidade de Brasília (UnB)** por meio da **prática esportiva.** Para isso, o sistema permite que os usuários se cadastrem e escolham entre três tipos de perfil  (Jogador, Torcedor ou Atlética), definindo assim a experiência inicial dentro do aplicativo. Após realizar o login, o usuário pode **visualizar informações sobre eventos**, **criar ou participar de partidas**, **seguir atléticas**, **criar e gerenciar times**, **acompanhar resultados** e **interagir com outros usuários** por meio de curtidas e comentários em postagens de eventos e partidas.


- **Influência na Arquitetura:**
A arquitetura escolhida foi a **Cliente-Servidor**, principalmente por permitir uma melhor **organização** entre as camadas de interface e de negócio, além de oferecer reutilização de serviços, **facilidade de manutenção** do código e possibilidade de **escalabilidade** futura. Nesse modelo, o cliente (aplicativo mobile desenvolvido em **React Native** com **Expo**) é responsável pela interface e interação com o usuário, enquanto o servidor (implementado em **Node.js**, com **Prisma** e **PostgreSQL** e **Express**) centraliza a lógica de negócio e fornece APIs REST para comunicação com o app.


- Dentre os requisitos que influenciaram a escolha do padrão de arquitetura:

    -Disponibilidade em **dispositivos móveis** (Android e iOS), facilitada pela utilização do React Native;

    -Necessidade de **autenticação** e **gerenciamento** centralizado de usuários

    -**Interação constante** com dados dinâmicos (eventos, times, postagens)

    -Facilidade de **manutenção** e **evolução** do sistema

    -Separação clara de **responsabilidades**

