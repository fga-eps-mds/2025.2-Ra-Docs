# Documentação do Projeto Aton - Grupo RÁ
![logo](assets/logo.svg){ width="700" }

> Este é o repositório oficial do projeto Aton, desenvolvido pelo grupo RÁ, como parte do curso de Engenharia de Software da Universidade de Brasília (UnB) na disciplina de Métodos de Desenvolvimento de Software. Aqui você encontrará todas as informações relevantes sobre o projeto, incluindo sua visão geral, arquitetura, planejamento e muito mais.

# O que é o Aton?
O Aton é um aplicativo móvel desenvolvido para centralizar a divulgação de eventos esportivos na Universidade de Brasília (UnB). Ele visa facilitar o acesso dos alunos às informações sobre eventos promovidos pelas atléticas e equipes amadoras, promovendo a integração da comunidade universitária por meio do esporte e do lazer.

### Lean inception para a concepção de produto e ideias:
<div>
    <iframe style="border: 1px solid rgba(0, 0, 0, 0.1);" width="800" height="450" src="https://embed.figma.com/board/OvHe9jFciGpdRbvyPZmsL4/Template-Lean-Inception?node-id=0-1&embed-host=share" allowfullscreen></iframe>
</div>

# Membros

<div class="team-container">
  {% for member in extra.team %}
    <a class="team-card" href="https://github.com/{{ member.username }}" target="_blank" rel="noopener">
      <img src="https://avatars.githubusercontent.com/{{ member.username }}?s=120" alt="{{ member.name }}">
      <p>{{ member.name }}</p>
    </a>
  {% endfor %}
</div>