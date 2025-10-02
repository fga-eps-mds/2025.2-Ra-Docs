# Escopo de projeto

## Backlog do produto

`TODO:` Adicionar descrição para o backlog do produto

## Perfis

Na idealização do projeto concebida por Lean inception todo usuário tem as mesmas permissões de acesso, exceto o usuário Administrador. Cada permissão é concedida a depender o fluxo de uso do usuário ao criar um grupo onde este usuário será terá permissões avançadas para aquele grupo. Após a tabela, mais especificações sobre a verificação e gestão de usuários.  

Tabela : Perfis de acesso 

| Nome do Perfil | Caracteristicas do perfil | permissões |
| --- | --- | --- |
| Usuário comum  | Cliente final a utilizar o aplicativo, sendo ativo em esportes ou apenas espectador de eventos promovidos por terceiros.  | Criar times/equipes, Acesso a publicações de usuários que ele segue.  |
| gerente de time  | Persona ativa em esportes que gerencia uma equipe amadora.  | Adicionar e remover pessoas à sua equipe + Permissões de Usuário comum  |    
| representante de atlética  | Persona cujo objetivo é representar a atlética e suas ações, bem como publicar eventos e competições.  | Ter um selo de verificado ao ser submetido a uma verificação de comprovação + permissões de gerente de time  |
| Administrador  | Responsável por manter os perfis de acesso da aplicação, criar novos usuários, alterar usuários já existentes, ou excluir usuários (Manter usuários). Além de verificar documentação de solicitação de criação de perfil de atlética.  | Permissão de gerenciar base de dados do sistema.  |


Na criação de um grupo/time/equipe, o usuário será submetido a uma classificação por exemplo para as atléticas, um representante da organização deverá criar uma equipe/grupo/time e ao preencher o formulário de criação deverá marcar uma opção como “é representante de uma atlética da UnB? Solicite sua verificação: [ ]” onde será enviado um link para um formulário externo para envio de documentação comprobatória que será analisada pela equipe de desenvolvimento. 

## Histórias de usuário

Tabela: Histórias de usuário (US)

| Numeração da US | Nome                           | Descrição                                                                                                                                   |
|-----------------|--------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| 1               | Autenticação e Gestão de Conta | Como um aluno que não pratica esporte, eu quero me cadastrar de forma rápida escolhendo o perfil "Torcedor", para que eu possa começar a acompanhar os jogos e eventos que me interessam. |
| 2               | Gerenciamento de Perfil de Usuário | Como um aluno esportista, eu quero adicionar ao meu perfil que eu sou "Ponta" no Vôlei com nível "Avançado", para que outros jogadores e times possam me encontrar com base nas minhas habilidades. |
| 3               | Visualização e Filtragem de Eventos | Como um aluno que gosta de acompanhar jogos, eu quero filtrar os eventos para ver apenas os jogos de Basquete que acontecerão no próximo fim de semana, para que eu possa me programar para assistir. |
| 4               | Criação e Gerenciamento de Eventos | Como uma Atlética da UnB, eu quero criar um post para um amistoso de Futsal contra outra atlética, para que todos os alunos da universidade possam saber do evento e comparecer. |
| 5               | Interação Social em Eventos    | Como um aluno torcedor, eu quero marcar "Eu vou" no post da final do campeonato, para que meus amigos vejam que estou interessado e a atlética tenha uma estimativa do público. |
| 6               | Sistema de Grupos e Times      | Como um aluno esportista, eu quero criar um grupo para o meu time de vôlei amador, para que eu possa organizar nossos treinos semanais e convidar novos membros facilmente. |
| 7               | Conexões e Notificações        | Como um aluno torcedor, eu quero seguir o perfil da minha atlética de curso, para que eu seja notificado sempre que eles publicarem um novo jogo ou evento. |
| 8               | Gamificação e Incentivo        | Como um aluno que não pratica esporte, eu quero ganhar emblemas por comparecer aos eventos, para que eu me sinta incentivado a participar mais e colecionar as conquistas. |
| 9               | Recrutamento de Jogadores      | Como um aluno esportista que é novo na universidade, eu quero marcar meu perfil como "Buscando Time" para a modalidade de Handebol, para que as equipes que precisam de jogadores possam me encontrar e me convidar. |
| 10              | Documentação para desenvolvedores | Como um desenvolvedor do UnB&Fit, eu quero contribuir em um ambiente organizado e trabalhar em tarefas bem definidas para que eu possa entregar o melhor código possível. |
| 11              |                                |                                                                                                                                             |

