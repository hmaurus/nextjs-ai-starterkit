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

#### 4.3. Atualizar CLAUDE.md

Substituir:
- `nome-do-projeto/` → `[NOME]/`
- Descrição do Starterkit → `[DESCRIÇÃO]`

#### 4.4. Substituir README.md

**Substituir** o README.md da raiz pelo README do novo projeto, baseado em @docs/projeto/.templates/README-template.md com dados do projeto.

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

#### 4.6. Commit e Push

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
  - CLAUDE.md
  - README.md
  - .env

Próximos passos:
1. Instalar plugin PDIR: /plugin install pdir-workflow@hmaurus/pdir-workflow-plugin
2. Criar PRD: /pdir-criar-prd "[DESCRIÇÃO]"
3. pnpm create next-app@latest . --typescript --tailwind --app
4. /dev-start
```
