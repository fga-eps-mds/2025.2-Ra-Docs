# Como contribuir no MkDocs

crie um ambiente virtual (recomendado)

```bash
python -m venv .venv
```
ative
```bash
# windows
.venv\Scripts\activate
# linux / mac
source .venv/bin/activate
```
instale as dependências em requirements.txt
```bash 
pip install -r requirements.txt
```
inicie o servidor de desenvolvimento
```bash
mkdocs serve
```
acesse o endereço http://localhost:8000
>>>>>>> docs

## Especificações Técnicas do Repositório

Usa a ferramenta MkDocs para gerar documentação baseado nos seus arquivos markdowns, mais instruções sobre o MkDocs através do link da documentação da ferramenta: [https://www.mkdocs.org/](https://www.mkdocs.org/).

Também é usado uma "sub-ferramenta" do MkDocs para sua estilização, o Material Theme, que pode ser consultado através do link: [https://squidfunk.github.io/mkdocs-material/](https://squidfunk.github.io/mkdocs-material/).
