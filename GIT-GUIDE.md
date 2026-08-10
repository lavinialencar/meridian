# 📦 Levando o Meridian pro Git

Guia direto do zero até push, com decisões justificadas. **Assume que você já tem git e conta no GitHub**.

---

## 1. Preparar a pasta

```bash
# Cria uma pasta pro projeto
mkdir meridian && cd meridian

# Copia o arquivo pra dentro (renomeando pra index.html se quiser servir via Pages)
cp ~/Downloads/meridian.html ./meridian.html
# OU pra deploy no GitHub Pages:
cp ~/Downloads/meridian.html ./index.html
```

---

## 2. Inicializar git

```bash
git init
git branch -M main
```

O `-M main` renomeia o branch default de `master` pra `main` (convenção moderna).

---

## 3. `.gitignore`

Cria `.gitignore` na raiz:

```gitignore
# OS
.DS_Store
Thumbs.db

# Editors
.vscode/
.idea/
*.swp

# Backups locais (o Meridian salva no Drive, mas se você exportar manualmente)
oraculo-backup-*.json
*.backup

# Logs
*.log
```

---

## 4. Adicionar README + LICENSE

Copia os arquivos que gerei:
```bash
cp ~/Downloads/README.md ./README.md
```

**LICENSE** (MIT — cria o arquivo `LICENSE` com este conteúdo, ajustando o ano/nome):

```
MIT License

Copyright (c) 2026 Lavínia Alencar

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

## 5. Primeiro commit

```bash
git add .
git status  # confere que só tem o que você quer (index.html, README.md, LICENSE, .gitignore)

git commit -m "feat: Meridian v1 — sistema de produtividade pessoal single-file

- Views: Home, Agenda, Tasks (lista+kanban), Foco, Stats, Links, Trash
- Backup automático no Google Drive (opcional via MCP)
- Themes dark/light, keyboard shortcuts, drag-to-reorder projetos
- Storage: localStorage schema oraculo-v8 com migração
- ~100KB JS + ~20KB CSS, zero build, zero dependências"
```

---

## 6. Criar repo no GitHub

**Opção A: via `gh` CLI** (mais rápido)
```bash
# Se ainda não tem gh: brew install gh (macOS) e depois `gh auth login`

# Cria repo público e faz push
gh repo create meridian --public --source=. --remote=origin --push

# OU privado (recomendado se contém dados sensíveis)
gh repo create meridian --private --source=. --remote=origin --push
```

**Opção B: via web**
1. github.com/new → nome `meridian`, descrição curta, público ou privado, **sem** README/gitignore/license (você já tem)
2. Copia a URL SSH ou HTTPS
3. No terminal:
   ```bash
   git remote add origin git@github.com:SEU-USUARIO/meridian.git
   git push -u origin main
   ```

---

## 7. Ativar GitHub Pages (opcional — pra usar via URL pública)

Se você renomeou pra `index.html`:

1. **GitHub → Settings → Pages**
2. Source: `Deploy from a branch`
3. Branch: `main` / folder: `/ (root)`
4. Save

Em ~1 minuto vai estar em `https://SEU-USUARIO.github.io/meridian/`

**⚠️ Aviso**: como o Meridian usa localStorage, cada domínio tem seus próprios dados. Se você usar tanto local quanto Pages, são bases separadas. Use um só.

---

## 8. Workflow diário sugerido

```bash
# Editou algo?
git status
git diff  # confere o que mudou

git add meridian.html   # ou . se quiser tudo
git commit -m "feat: adiciona X"  # ou fix:, refactor:, docs:, style:
git push
```

**Convenção de commits** (Conventional Commits):
- `feat:` nova feature
- `fix:` bug
- `refactor:` melhoria de código sem mudar comportamento
- `perf:` performance
- `docs:` só documentação
- `style:` CSS/formatação
- `chore:` build/config

Exemplo real do que fizemos:
```
git commit -m "perf: memoize tat() e refatora rfc() pra evitar 325 spreads/render"
git commit -m "feat: color picker com 12 swatches por projeto"
git commit -m "refactor: remove integração Jira (uso pessoal)"
```

---

## 9. Branch strategy (quando quiser experimentar)

```bash
# Cria branch pra uma nova feature
git checkout -b feat/tags-em-tasks

# Trabalha, commita
git add . && git commit -m "feat: adiciona sistema de tags em tasks"

# Volta pra main e faz merge quando estiver ok
git checkout main
git merge feat/tags-em-tasks

# Deleta a branch de trabalho
git branch -d feat/tags-em-tasks
```

Se der ruim: `git checkout main` e simplesmente delete a branch com `-D` (force). Sem estresse.

---

## 10. Backup do backup

Como você já tem backup no Google Drive rodando dentro do Meridian, mais dois lugares:

- **GitHub** guarda todas as versões do CÓDIGO
- **Drive** guarda o snapshot dos DADOS (localStorage exportado)

Combinação = você pode perder o computador e recuperar 100% em 5min.

**Não commite** os JSON de backup (`.gitignore` já barra). Se quiser incluir um exemplo de estrutura, cria um `example-data.json` com dados fictícios.

---

## 11. Se quiser publicar como "portfolio"

Adiciona no topo do README:

```markdown
🌐 **Demo**: https://SEU-USUARIO.github.io/meridian/
📝 **Blog post**: [link opcional]
🎥 **Screencast**: [link opcional]
```

E screenshots (drop `screenshots/*.png` na pasta, referencia com `![Home](screenshots/home.png)`).

**Social preview do repo** (a imagem que aparece quando o link é compartilhado): isso não dá pra fazer por API, precisa subir manual. `branding/social-preview.png` já está pronto — é só ir em **GitHub → Settings → General → Social preview → Upload an image** e escolher esse arquivo.

---

## 12. Checklist final antes do push inicial

- [ ] `.gitignore` no lugar
- [ ] `README.md` sem informações sensíveis
- [ ] Nenhum dado pessoal hardcoded no código (você já tirou tudo do Jira)
- [ ] `LICENSE` (MIT)
- [ ] Testou o arquivo direto no browser? Funciona standalone?
- [ ] `git log --oneline` mostra o commit inicial bonito?

Bora.

---

## Comandos de emergência

**Errei o último commit** (mensagem ou arquivo esquecido):
```bash
git add arquivo-esquecido
git commit --amend --no-edit   # mantém mensagem
# ou
git commit --amend -m "nova mensagem"
```

**Committei algo sensível** (senha, token):
```bash
# Se AINDA não deu push
git reset --soft HEAD~1  # desfaz commit, mantém mudanças
# tira o segredo, commita de novo

# Se JÁ deu push: use BFG Repo-Cleaner ou git filter-repo
# https://rtyley.github.io/bfg-repo-cleaner/
```

**Ver o que mudou desde o último commit**:
```bash
git diff              # não-staged
git diff --staged     # staged
git log --oneline -10 # últimos 10 commits
```

**Reverter arquivo pro último commit**:
```bash
git checkout -- meridian.html
```

**Voltar toda a working copy pra HEAD** (⚠️ perde mudanças não commitadas):
```bash
git reset --hard HEAD
```
