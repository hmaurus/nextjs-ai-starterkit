---
description: Wizard de configuração inicial do projeto
argument-hint: (interativo)
---

# Configurar Projeto

Configura o projeto após clonar o pdir-workflow.

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
3. **APP_SLOT** - número único (0, 1, 2...) para calcular portas; consultar slots em uso em `/home/mhenriques/mhtec/pc-setup/common/portas-em-uso.md`

Validar nome: apenas letras minúsculas, números e hífens.
Validar slot: número ≥0, não conflitar com slots existentes.

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

#### 4.4. Gerar docs/projeto/PRD.md

Gerar PRD baseado em @docs/projeto/.templates/PRD-template.md usando a descrição do usuário.

**Diretrizes:**
- Tom profissional e direto
- Foco em MVP (3-7 funcionalidades)
- Inferências razoáveis apenas
- Documento enxuto (1-2 páginas)

#### 4.5. Substituir README.md

**Substituir** o README.md da raiz (manual do starter kit) pelo README do novo projeto, baseado em @docs/projeto/.templates/README-template.md com dados do projeto (sem informações redundantes já presentes no CLAUDE.md).

> O manual do starter kit fica em `docs/manuais/starterkit-pdir.md`.

#### 4.6. Gerar .env

Criar `.env` baseado em `.env.example` com portas calculadas:

| Serviço       | Base  | Fórmula           |
|---------------|-------|-------------------|
| Next.js       | 3000  | 3000 + (SLOT×10)  |
| Prisma Studio | 5555  | 5555 + (SLOT×10)  |
| Supabase API  | 54321 | 54321 + (SLOT×10) |
| Supabase DB   | 54322 | 54322 + (SLOT×10) |
| Supabase Studio | 54323 | 54323 + (SLOT×10) |
| Inbucket      | 54324 | 54324 + (SLOT×10) |

Registrar o novo projeto em `/home/mhenriques/mhtec/pc-setup/common/portas-em-uso.md`.

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
  - CLAUDE.md
  - docs/projeto/PRD.md
  - README.md

Próximos passos:
1. Revisar PRD.md e README.md
2. pnpm create next-app@latest . --typescript --tailwind --app
3. cp .env.example .env
4. /dev-start
```
