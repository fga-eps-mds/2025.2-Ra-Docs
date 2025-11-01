## 2.3 Arquitetura x Produto

### 2.3.1 Metas e restrições arquiteturais
> Levantar e justificar metas de desempenho, disponibilidade, segurança e padrões de codificação. Pode usar tabela de metas e justificativas.

entrega: `Texto + tabela`


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

