# Next.js AI Starterkit + Método PDIR

Manual do starter kit e método PDIR para criar aplicativos fullstack Next.js com IA.

**Autor:** Maurus Henriques - maurus@maurus.com.br

---

## Parte 1: Starter Kit

### O que é este kit?

Template que fornece:

- Configuração do Claude Code (comandos + MCP servers)
- CLAUDE.md com regras para projetos Next.js
- Comandos de desenvolvimento (`/dev-start`, `/dev-stop`)
- **NÃO** inclui Next.js (você instala conforme necessidade)

### Pré-requisitos

- Node.js 20+ | pnpm | Docker (opcional) | Git | GitHub CLI (`gh`) | Claude Code CLI

### Setup Rápido

```bash
# 1. Clonar
git clone git@github.com:hmaurus/nextjs-ai-starterkit.git seu-projeto
cd seu-projeto

# 2. Autenticar GitHub CLI (se ainda não fez)
gh auth login

# 3. Abrir Claude Code
claude .

# 4. Executar wizard
/configurar-projeto
```

O wizard:
- Cria repositório GitHub privado
- Atualiza CLAUDE.md com dados do projeto
- Gera .env com portas baseadas no APP_SLOT
- Substitui README.md

### Após Configuração

```bash
# Instalar Next.js
pnpm create next-app@latest . --typescript --tailwind --app

# Iniciar desenvolvimento
/dev-start
```

---

## Parte 2: Plugin PDIR (Opcional)

O PDIR (Planejar, Dividir, Implementar, Revisar) é um método para desenvolver software de forma estruturada usando IA.

### Instalação do Plugin

```bash
/plugin install pdir-workflow@hmaurus-masterclaude
```

### Fluxo PDIR

```
PRD.md → Grupos → Tarefas → Issue → Implementar → Commit → PR → Merge
```

### Fase 0: Criar o PRD

```bash
/pdir-criar-prd "Descrição do seu projeto"
```

**Resultado:** `docs/projeto/PRD.md`

### Fase 1: Dividir o PRD em Grupos

```bash
/pdir-listar-grupos PRD.md subgrupos
```

**Resultado:** `docs/projeto/grupos/lista-grupos-PRD.md`

Estrutura hierárquica com grupos e subgrupos:

```markdown
## Grupo: Autenticação
### Subgrupo: Login
### Subgrupo: Registro

## Grupo: Dashboard
### Subgrupo: Visão Geral
```

**Dica:** Use `grupos` em vez de `subgrupos` para projetos menores.

### Fase 2: Gerar Tarefas por Grupo

Para **cada** grupo ou subgrupo:

```bash
/pdir-listar-tarefas grupos/lista-grupos-PRD.md "Subgrupo: Login"
```

**Resultado:** `docs/projeto/tarefas/lista-tarefas-login.md`

### Fase 3: Implementar Tarefas (Ciclo)

Para **cada** tarefa da lista:

```bash
# 1. Criar Issue
/pdir-criar-issue feat(auth): criar página de login

# 2. Implementar (cria branch feat/42-...)
/pdir-implementar-tarefa 42

# 3. Commitar (pode executar várias vezes)
/pdir-commit

# 4. Criar Draft PR
/pdir-draft-pr

# 5. Marcar pronto e fazer merge
/pdir-ready-pr
/pdir-merge-tarefa
```

**Resultado:** PR merged, branch deletada, Issue fechada.

### Resumo dos Comandos PDIR

| Fase | Comando | O que faz |
|------|---------|-----------|
| PRD | `/pdir-criar-prd` | descrição → PRD.md |
| Dividir | `/pdir-listar-grupos` | PRD → grupos/subgrupos |
| Tarefas | `/pdir-listar-tarefas` | grupo → lista de tarefas |
| Issue | `/pdir-criar-issue` | tarefa → Issue GitHub |
| Implementar | `/pdir-implementar-tarefa` | Issue → branch + código |
| Commit | `/pdir-commit` | commit + push |
| PR | `/pdir-draft-pr` | cria Draft PR |
| Ready | `/pdir-ready-pr` | marca PR pronto |
| Merge | `/pdir-merge-tarefa` | merge + limpeza |

### Fluxo Visual

```
┌─────────────────────────────────────────────────────────────┐
│                    /pdir-criar-prd                          │
└─────────────────────────┬───────────────────────────────────┘
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                        PRD.md                               │
└─────────────────────────┬───────────────────────────────────┘
                          │ /pdir-listar-grupos
                          ▼
┌─────────────────────────────────────────────────────────────┐
│              lista-grupos-PRD.md                            │
│  ├── Grupo: Auth                                            │
│  │   ├── Subgrupo: Login                                    │
│  │   └── Subgrupo: Registro                                 │
│  └── Grupo: Dashboard                                       │
└─────────────────────────┬───────────────────────────────────┘
                          │ /pdir-listar-tarefas (por subgrupo)
                          ▼
┌─────────────────────────────────────────────────────────────┐
│              lista-tarefas-login.md                         │
│  - feat(auth): criar página de login                        │
│  - feat(auth): validar credenciais                          │
└─────────────────────────┬───────────────────────────────────┘
                          │ Para cada tarefa:
                          ▼
┌─────────────────────────────────────────────────────────────┐
│  /pdir-criar-issue                                          │
│  /pdir-implementar-tarefa [issue]                           │
│  /pdir-commit (1 ou mais vezes)                             │
│  /pdir-draft-pr                                             │
│  /pdir-ready-pr                                             │
│  /pdir-merge-tarefa                                         │
└─────────────────────────────────────────────────────────────┘
```

---

## Parte 3: Comandos do Starterkit

| Comando | Função |
|---------|--------|
| `/configurar-projeto` | Wizard inicial (cria repo, .env) |
| `/dev-start` | Inicia Next.js + Prisma Studio |
| `/dev-stop` | Para serviços |
| `/dev-restart` | Reinicia serviços |
| `/planejar-e-implementar` | Tarefa avulsa (fora do PDIR) |

Portas: configuradas via APP_SLOT no `.env`

---

## Parte 4: Estrutura de Arquivos

```
seu-projeto/
├── CLAUDE.md                    # Regras do projeto
├── README.md                    # README do seu projeto
├── .env                         # Variáveis de ambiente (gerado)
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
│       ├── PRD.md               # Plano geral (via plugin)
│       ├── grupos/              # Lista de grupos (gerados)
│       └── tarefas/             # Lista de tarefas (gerados)
└── src/                         # Código (após instalar Next.js)
```

---

## Dicas

- **Granularidade:** Uma tarefa = uma funcionalidade testável isoladamente
- **Commits:** Commite frequentemente, não espere terminar tudo
- **PRD primeiro:** Invista tempo no PRD antes de começar a dividir
- **Sequência:** Respeite dependências entre tarefas ao implementar

---

## Links

- **Plugin PDIR:** https://github.com/hmaurus/masterclaude
- **Starterkit:** https://github.com/hmaurus/nextjs-ai-starterkit

---

**Desenvolvido com Claude Code**
