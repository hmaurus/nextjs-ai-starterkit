# Next.js AI Starterkit + Metodo PDIR

Manual do starter kit e metodo PDIR para criar aplicativos fullstack Next.js com IA.

**Autor:** Maurus Henriques - maurus@maurus.com.br

---

## Parte 1: Starter Kit

### O que e este kit?

Template que fornece:

- Configuracao do Claude Code
- CLAUDE.md com regras para projetos Next.js
- Comandos de desenvolvimento (`/dev-start`, `/dev-stop`)
- **NAO** inclui Next.js (voce instala conforme necessidade)

### Pre-requisitos

- Node.js 20+ | pnpm | Docker (opcional) | Git | GitHub CLI (`gh`) | Claude Code CLI

### Setup Rapido

```bash
# 1. Clonar
git clone git@github.com:hmaurus/nextjs-ai-starterkit.git seu-projeto
cd seu-projeto

# 2. Autenticar GitHub CLI (se ainda nao fez)
gh auth login

# 3. Abrir Claude Code
claude .

# 4. Executar wizard
/configurar-projeto
```

O wizard:
- Cria repositorio GitHub privado
- Atualiza CLAUDE.md com dados do projeto
- Gera .env com portas baseadas no APP_SLOT
- Substitui README.md

### Apos Configuracao

```bash
# Instalar Next.js
pnpm create next-app@latest . --typescript --tailwind --app

# Iniciar desenvolvimento
/dev-start
```

---

## Parte 2: Plugin PDIR (Opcional)

O PDIR (Planejar, Dividir, Implementar, Revisar) e um metodo para desenvolver software de forma estruturada usando IA.

### Instalacao do Plugin

```bash
/plugin marketplace add hmaurus/masterclaude
/plugin install pdir-workflow@hmaurus-masterclaude
```

### Fluxo PDIR

```
PRD.md -> Tarefas -> Issue -> Implementar -> Commit -> PR -> Merge
```

### Fase 0: Criar o PRD

O plugin foca no ciclo a partir da divisao em tarefas. Para criar o PRD (`docs/projeto/PRD.md`), use uma destas abordagens:

- **`/doc-coauthoring`** -- co-autoria interativa com entrevista, refinamento e teste de clareza (requer skill `doc-coauthoring`)
- **`/brainstorming`** -- exploracao criativa de requisitos antes de estruturar o PRD
- **Conversa direta** -- descreva o projeto ao Claude e peca para criar o PRD em `docs/projeto/PRD.md`

### Fase 1: Dividir em Tarefas

```bash
# PRD completo
/pdir-dividir-em-tarefas docs/projeto/PRD.md

# Secao especifica do PRD
/pdir-dividir-em-tarefas docs/projeto/PRD.md#Fase 1 - Fundacao

# Descricao livre (sem PRD)
/pdir-dividir-em-tarefas Sistema de notificacoes com email e SMS
```

**Resultado:** `docs/projeto/tarefas/lista-tarefas-[nome].md`

Cada tarefa e atomica, testavel e dimensionada para uma sessao de AI Coding (~1-8 arquivos).

### Fase 2: Implementar Tarefas (Ciclo)

Para **cada** tarefa da lista:

```bash
# 1. Criar Issue (a partir do arquivo de tarefas)
/pdir-criar-issue docs/projeto/tarefas/lista-tarefas-setup.md#configurar eslint

# 1b. Ou criar Issue com descricao livre
/pdir-criar-issue adicionar validacao de email no cadastro

# 2. Implementar (cria branch feat/42-...)
/pdir-implementar-tarefa-com-branch 42

# 3. Commitar (pode executar varias vezes)
/pdir-commit

# 4. Criar PR vinculado a Issue
/pdir-criar-pr

# 5. Fazer merge quando aprovado
/pdir-merge-tarefa
```

**Resultado:** PR merged, branch deletada, Issue fechada.

**Variante sem branch:** use `/pdir-implementar-tarefa 42` para implementar direto na branch atual (util para tarefas menores ou quando ja esta numa branch de feature).

### Entre Tarefas

```bash
# Sugere proxima tarefa com base no contexto
/pdir-proxima-tarefa
```

Analisa o PRD, Issues abertas/fechadas e o que foi feito na sessao atual para sugerir a proxima tarefa logica.

### Resumo dos Comandos PDIR

| Fase | Comando | O que faz |
|------|---------|-----------|
| Dividir | `/pdir-dividir-em-tarefas` | PRD ou descricao -> lista de tarefas |
| Issue | `/pdir-criar-issue` | tarefa -> Issue GitHub (com labels e milestone) |
| Sugestao | `/pdir-proxima-tarefa` | sugere proxima tarefa com base no contexto |
| Implementar | `/pdir-implementar-tarefa-com-branch` | Issue -> branch + codigo |
| Implementar | `/pdir-implementar-tarefa` | Issue -> codigo (branch atual) |
| Commit | `/pdir-commit` | commit + push (Conventional Commits) |
| PR | `/pdir-criar-pr` | cria PR vinculado a Issue |
| Merge | `/pdir-merge-tarefa` | squash merge + limpeza |

### Fluxo Visual

```
┌─────────────────────────────────────────────────────────────┐
│            Criar PRD em docs/projeto/PRD.md                 │
│    (conversa direta, /doc-coauthoring ou /brainstorming)    │
└─────────────────────────┬───────────────────────────────────┘
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                        PRD.md                               │
└─────────────────────────┬───────────────────────────────────┘
                          │ /pdir-dividir-em-tarefas
                          ▼
┌─────────────────────────────────────────────────────────────┐
│         lista-tarefas-[nome].md                             │
│  - feat(auth): criar pagina de login                        │
│  - feat(auth): validar credenciais                          │
└─────────────────────────┬───────────────────────────────────┘
                          │ Para cada tarefa:
                          ▼
┌─────────────────────────────────────────────────────────────┐
│  /pdir-criar-issue                                          │
│  /pdir-implementar-tarefa-com-branch [issue]                │
│  /pdir-commit (1 ou mais vezes)                             │
│  /pdir-criar-pr                                             │
│  /pdir-merge-tarefa                                         │
│                                                             │
│  /pdir-proxima-tarefa (entre tarefas)                       │
└─────────────────────────────────────────────────────────────┘
```

---

## Parte 3: Comandos do Starterkit

| Comando | Funcao |
|---------|--------|
| `/configurar-projeto` | Wizard inicial (cria repo, .env) |
| `/dev-start` | Inicia Next.js + Prisma Studio |
| `/dev-stop` | Para servicos |
| `/dev-restart` | Reinicia servicos |
| `/planejar-e-implementar` | Tarefa avulsa (fora do PDIR) |

Portas: configuradas via APP_SLOT no `.env`

---

## Parte 4: Estrutura de Arquivos

```
seu-projeto/
├── CLAUDE.md                    # Regras do projeto
├── README.md                    # README do seu projeto
├── .env                         # Variaveis de ambiente (gerado)
├── .claude/commands/            # Comandos do starterkit
│   ├── configurar-projeto.md
│   ├── planejar-e-implementar.md
│   └── servidor/
├── docs/
│   ├── .templates/              # Templates do starterkit
│   │   ├── README-template.md
│   │   └── components.json
│   ├── manuais/                 # Este manual
│   └── projeto/                 # Arquivos gerados pelo PDIR
│       ├── PRD.md               # Plano geral (manual ou assistido)
│       └── tarefas/             # Lista de tarefas (gerados)
└── src/                         # Codigo (apos instalar Next.js)
```

---

## Dicas

- **Granularidade:** Uma tarefa = uma funcionalidade testavel isoladamente
- **Commits:** Commite frequentemente, nao espere terminar tudo
- **PRD primeiro:** Invista tempo no PRD antes de comecar a dividir
- **Sequencia:** Respeite dependencias entre tarefas ao implementar
- **Contexto limpo:** Execute `/clear` entre tarefas para manter o contexto enxuto

---

## Links

- **Plugin PDIR:** https://github.com/hmaurus/masterclaude
- **Starterkit:** https://github.com/hmaurus/nextjs-ai-starterkit

---

**Desenvolvido com Claude Code**
