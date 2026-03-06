---
description: Wizard de configuração inicial do projeto
argument-hint: (interativo)
---

# Configurar Projeto

Configura o projeto após clonar o starterkit.

## Instruções

### 1. Verificar Pré-requisitos

```bash
gh auth status
```

**Se não autenticado:** Informar para executar `gh auth login` e abortar.

### 2. Coletar Informações

Usar `AskUserQuestion` para coletar:

1. **Nome do projeto** - sugerir nome do diretório atual (`basename $(pwd)`)
2. **Descrição** - parágrafo breve (objetivo, público-alvo, funcionalidades)
3. **APP_SLOT** - número único (0, 1, 2...) para calcular portas

Validar nome: apenas letras minúsculas, números e hífens.
Validar slot: número ≥0.

### 3. Confirmar

Exibir resumo e pedir confirmação antes de executar.

### 4. Executar Setup

#### 4.1. Reinicializar Git

```bash
git remote remove origin 2>/dev/null || true
rm -rf .git && git init && git branch -M main
```

#### 4.2. Criar Repositório GitHub

```bash
gh repo create "[NOME]" --private --description "[DESCRIÇÃO]" --source=. --remote=origin
```

Capturar URL: `gh repo view --json url -q .url`

#### 4.3. Configurar CLAUDE.md

Usar `AskUserQuestion` para perguntar ao usuário qual modelo de CLAUDE.md deseja:

**Opção 1 — Completo (padrão):**
Manter o CLAUDE.md atual (autossuficiente, tudo em um arquivo).
- Aplicar substituições de nome/descrição no CLAUDE.md
- Apagar `docs/claude-md/`

**Opção 2 — Separar global + projeto:**
Usar regras genéricas como global e manter apenas regras específicas no projeto.
- Substituir `/CLAUDE.md` pelo conteúdo de `docs/claude-md/CLAUDE-next.md`
- Aplicar substituições de nome/descrição no novo CLAUDE.md
- Se `~/.claude/CLAUDE.md` já existir, fazer backup para `~/.claude/CLAUDE.md.bak` e avisar o usuário
- Copiar `docs/claude-md/CLAUDE-global.md` para `~/.claude/CLAUDE.md`
- Apagar `docs/claude-md/`

**Opção 3 — Só projeto (já tenho global):**
Já possui `~/.claude/CLAUDE.md` próprio e quer apenas o CLAUDE.md enxuto do projeto.
- Verificar que `~/.claude/CLAUDE.md` existe; se não existir, avisar e sugerir opção 2
- Substituir `/CLAUDE.md` pelo conteúdo de `docs/claude-md/CLAUDE-next.md`
- Aplicar substituições de nome/descrição no novo CLAUDE.md
- Apagar `docs/claude-md/`

**Substituições (todas as opções):**
- `nome-do-projeto/` → `[NOME]/`
- Descrição do Starterkit → `[DESCRIÇÃO]`

#### 4.4. Substituir README.md

**Substituir** o README.md da raiz pelo README do novo projeto, baseado em @docs/.templates/README-template.md com dados do projeto.

#### 4.5. Gerar .env

Criar `.env` baseado em `.env.example` com portas calculadas:

| Serviço       | Base  | Fórmula           |
|---------------|-------|-------------------|
| Next.js       | 3000  | 3000 + (SLOT×10)  |
| Prisma Studio | 5555  | 5555 + (SLOT×10)  |
| Supabase API  | 54321 | 54321 + (SLOT×10) |
| Supabase DB   | 54322 | 54322 + (SLOT×10) |
| Supabase Studio | 54323 | 54323 + (SLOT×10) |
| Inbucket      | 54324 | 54324 + (SLOT×10) |

#### 4.6. Atualizar package.json

Substituir o campo `name` pelo nome do projeto:
- `"name": "nextjs-ai-starterkit"` → `"name": "[NOME]"`

#### 4.7. Commit e Push

```bash
git add -A

git commit -m "$(cat <<'EOF'
chore: initial commit via /configurar-projeto

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
EOF
)"

git push -u origin main
```

### 5. Feedback Final

```
✅ Projeto configurado!

📦 Nome: [NOME]
🔗 GitHub: [URL] (privado)

📁 Arquivos atualizados:
  - CLAUDE.md (+ ~/.claude/CLAUDE.md se opção 2)
  - README.md
  - package.json
  - .env
  - docs/claude-md/ (removido)

Próximos passos:
1. Adicionar marketplace: /plugin marketplace add hmaurus/masterclaude
2. Instalar plugin PDIR: /plugin install pdir-workflow@hmaurus-masterclaude
3. pnpm create next-app@latest . --typescript --tailwind --app
4. /dev-start
5. Criar PRD em docs/projeto/PRD.md (via conversa, /doc-coauthoring ou /brainstorming)
6. /pdir-dividir-em-tarefas docs/projeto/PRD.md
```
