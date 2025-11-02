# 2. Arquitetura

## 2.1 Definições Gerais

### 2.1.1 Definições

## Estilo Arquitetural Adotado: Cliente-Servidor

O sistema adota o **estilo arquitetural cliente-servidor**, amplamente utilizado em aplicações que exigem comunicação estruturada entre uma interface de usuário e um servidor central responsável pelo processamento e persistência dos dados.

Nesse modelo, existe uma **divisão clara de responsabilidades**:

- O **cliente** é responsável pela interação com o usuário e pela coleta/envio de informações.  

- O **servidor** executa as regras de negócio, processa as requisições e gerencia o acesso seguro aos dados.

Essa separação facilita a **escalabilidade, manutenção e evolução** do sistema, permitindo que as camadas sejam desenvolvidas e atualizadas de forma independente.

## Componentes Conceituais do Sistema

1. **Frontend (Cliente):** 

      - Desenvolvido em **React Native**, possibilitando o uso do sistema em **dispositivos móveis (Android e iOS)**.
   - Responsável pela interface e experiência do usuário (UI/UX), com base em protótipos elaborados no **Figma**.
   - Consome os serviços do backend por meio de **requisições HTTP/HTTPS**, trocando dados no formato **JSON**.
   - Implementa as principais funcionalidades de interação, como autenticação, exibição de dados e envio de formulários.

2. **Backend (Servidor):**

      - Implementa uma **API RESTful**, centralizando as regras de negócio e o controle de acesso.
   - Recebe requisições dos clientes, processa as informações e retorna respostas estruturadas.
   - Gerencia a lógica de autenticação, autorização e persistência de dados.
   - Utiliza padrões de organização como **MVC (Model-View-Controller)** ou equivalentes, garantindo modularidade e clareza.
   - Interage com o banco de dados via **Prisma ORM**, simplificando consultas, migrações e manutenção do esquema de dados.

3. **Banco de Dados:**

      - Utiliza o **PostgreSQL**, um sistema gerenciador de banco de dados relacional (SGBD) robusto e open source.
   - Responsável pelo **armazenamento seguro e persistente** das informações do sistema.
   - Oferece recursos avançados como **integridade referencial**, **transações ACID** e **consultas otimizadas**.
   - O **Prisma ORM** atua como camada de abstração, permitindo que o backend execute operações no banco de dados de forma segura, tipada e eficiente.

4. **Camada de Comunicação:**

      - A comunicação entre cliente e servidor é feita via **protocolo HTTP/HTTPS**.
   - Segue o padrão **REST**, com endpoints organizados por recursos.
   - Os dados trafegam em formato **JSON**, garantindo compatibilidade entre plataformas.
   - A autenticação é realizada por meio de **tokens JWT**, assegurando sessões seguras e controle de permissões.

## Benefícios da Arquitetura Cliente-Servidor

- **Separação de responsabilidades:** frontend e backend podem evoluir de forma independente.  
- **Escalabilidade:** o servidor pode ser replicado para atender múltiplas conexões simultâneas.  
- **Portabilidade:** o uso do React Native permite acesso multiplataforma (Android e iOS).  
- **Segurança:** as regras de negócio e o controle de acesso permanecem centralizados no servidor.  
- **Padronização:** o uso de HTTP/HTTPS, JSON e REST facilita integração com outros serviços.  
- **Produtividade e manutenção facilitada:** o uso do Prisma ORM permite consultas tipadas, migrações automáticas e menor risco de erros em operações de banco de dados.

> **Resumo:**  
> O modelo cliente-servidor foi adotado por proporcionar uma estrutura modular, segura e escalável.  
> A combinação de **React Native** para o cliente, **PostgreSQL + Prisma** para persistência de dados e **Figma** como ferramenta de design oferece uma base sólida para o desenvolvimento e evolução do sistema.

### 2.1.2  Justificativa de escolha

# Motivo da escolha 
A arquitetura cliente-servidor foi escolhida por atender de forma eficiente aos requisitos funcionais e não funcionais definidos para o sistema. Esse modelo garante uma separação clara entre as responsabilidades de interface (frontend) e processamento de dados (backend). Esse modelo de organização separa claramente a  responsabilidade da apresentação com o processamento, o que permite que uma interface foque na total experiência do usuário e a outra concentre toda a lógica, persistência e autênticações. 

A escolha por esta arquitetura foi motivada pela necessidade de entregar um produto multiplataforma com rapidez, manter o controle centralizado das regras de negócio e reduzir a complexidade operacional na fase inicial do projeto. **O uso de PostgreSQL combinado com Prisma oferece persistência relacional robusta, migrações tipadas e segurança nas operações de banco de dados.**

Em resumo, a arquitetura cliente‑servidor equilibra rapidez de desenvolvimento, segurança e capacidade de evolução do Aton, oferecendo uma base pragmática e escalável para suportar as funcionalidades de gerenciamento de eventos, feed social e autenticação de usuários demandadas pelo sistema.

