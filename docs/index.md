

# Membros

<div class="team-container">
  {% for member in extra.team %}
    <a class="team-card" href="https://github.com/{{ member.username }}" target="_blank" rel="noopener">
      <img src="https://avatars.githubusercontent.com/{{ member.username }}?s=120" alt="{{ member.name }}">
      <p>{{ member.name }}</p>
    </a>
  {% endfor %}
</div>