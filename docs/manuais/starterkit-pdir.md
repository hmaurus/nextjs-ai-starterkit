# Starter Kit + Método PDIR Workflow

Manual completo do starter kit e método para criar aplicativos fullstack Next.js com IA.

**Autor:** Maurus Henriques - maurus@maurus.com.br

---

## Parte 1: Starter Kit

### O que é este kit?

Template que fornece:

- Configuração do Claude Code (comandos + MCP servers)
- Método PDIR para desenvolvimento estruturado com IA
- CLAUDE.md com regras para projetos Next.js
- **NÃO** inclui Next.js (você instala conforme necessidade)

### Pré-requisitos

- Node.js 20+ | pnpm | Docker (opcional) | Git | GitHub CLI (`gh`) | Claude Code CLI

### Setup Rápido

```bash
# 1. Clonar
git clone git@github.com:hmaurus/pdir-workflow.git seu-projeto
cd seu-projeto

# 2. Configurar repositório
git remote remove origin
git remote add origin [SUA_URL]
git push -u origin main

# 3. Autenticar GitHub CLI
gh auth login

# 4. Configurar MCP (opcional)
cp .mcp.json.example .mcp.json

# 5. Wizard de configuração
/configurar-projeto
```

O wizard cria o repositório GitHub, gera PRD.md e substitui o README.md.

### Após Configuração

```bash
pnpm create next-app@latest . --typescript --tailwind --app
cp .env.example .env
docker compose up -d postgres  # se usar Docker
/dev-start
```

---

## Parte 2: Método PDIR Workflow

O PDIR (Planejar, Dividir, Implementar, Revisar) é um método para desenvolver software de forma estruturada usando IA.

### Visão Geral do Fluxo

```
PRD.md → Grupos → Tarefas → Issue → Implementar → Commit → PR → Merge
```

### Fase 1: Dividir o PRD em Grupos

**Pré-requisito:** Ter um `docs/projeto/PRD.md` (gerado pelo `/configurar-projeto` ou criado manualmente).

```bash
/pdir:pdir-listar-grupos PRD.md subgrupos
```

**Resultado:** `docs/projeto/grupos/lista-grupos-PRD.md`

Estrutura hierárquica com grupos e subgrupos. Exemplo:

```markdown
## Grupo: Autenticação
### Subgrupo: Login
### Subgrupo: Registro
### Subgrupo: Recuperação de Senha

## Grupo: Dashboard
### Subgrupo: Visão Geral
### Subgrupo: Relatórios
```

**Dica:** Use `grupos` em vez de `subgrupos` para projetos menores.

### Fase 2: Gerar Tarefas por Grupo/Subgrupo

Para **cada** grupo ou subgrupo, execute:

```bash
/pdir:pdir-listar-tarefas grupos/lista-grupos-PRD.md "Subgrupo: Login"
```

**Resultado:** `docs/projeto/tarefas/lista-tarefas-login.md`

Lista de tarefas atômicas no formato `tipo(escopo): descrição`. Exemplo:

```markdown
- feat(auth): criar página de login
- feat(auth): implementar validação de credenciais
- feat(auth): adicionar autenticação com OAuth
- test(auth): testes unitários do login
```

**Repita** para cada grupo/subgrupo até ter todas as tarefas.

### Fase 3: Implementar Tarefas (Ciclo)

Para **cada** tarefa da lista:

#### 3.1. Criar Issue

```bash
/pdir:pdir-criar-issue feat(auth): criar página de login
```

Ou selecione o texto da tarefa na IDE e execute `/pdir:pdir-criar-issue`.

#### 3.2. Implementar

```bash
/pdir:pdir-implementar-tarefa 42
```

Cria branch `feat/42-criar-pagina-login` e implementa a funcionalidade.

#### 3.3. Commitar

```bash
/pdir:pdir-commit
```

Pode executar múltiplas vezes durante a implementação (commits incrementais).

#### 3.4. Criar Draft PR

```bash
/pdir:pdir-draft-pr
```

#### 3.5. Continuar Desenvolvimento (opcional)

```bash
/pdir:pdir-commit  # mais commits se necessário
```

#### 3.6. Marcar Pronto e Fazer Merge

```bash
/pdir:pdir-ready-pr
/pdir:pdir-merge-tarefa
```

**Resultado:** PR merged, branch deletada, Issue fechada.

### Resumo dos Comandos

| Fase | Comando | O que faz |
|------|---------|-----------|
| Dividir | `/pdir:pdir-listar-grupos` | PRD → grupos/subgrupos |
| Tarefas | `/pdir:pdir-listar-tarefas` | grupo → lista de tarefas |
| Issue | `/pdir:pdir-criar-issue` | tarefa → Issue GitHub |
| Implementar | `/pdir:pdir-implementar-tarefa` | Issue → branch + código |
| Commit | `/pdir:pdir-commit` | commit + push |
| PR | `/pdir:pdir-draft-pr` | cria Draft PR |
| Ready | `/pdir:pdir-ready-pr` | marca PR pronto |
| Merge | `/pdir:pdir-merge-tarefa` | merge + limpeza |

### Fluxo Visual

```
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
│  /pdir-commit (opcional)                                    │
│  /pdir-ready-pr                                             │
│  /pdir-merge-tarefa                                         │
└─────────────────────────────────────────────────────────────┘
```

---

## Parte 3: Comandos de Desenvolvimento

| Comando | Função |
|---------|--------|
| `/dev-start` | Inicia Next.js + Prisma Studio |
| `/dev-stop` | Para serviços |
| `/dev-restart` | Reinicia serviços |
| `/configurar-projeto` | Wizard inicial |
| `/planejar-e-implementar` | Tarefa avulsa (fora do PDIR) |

Portas: ver `.env.example`

---

## Parte 4: Estrutura de Arquivos

```
seu-projeto/
├── CLAUDE.md                    # Regras do projeto (fonte de verdade)
├── README.md                    # README do seu projeto
├── .claude/
│   ├── commands/pdir/           # Comandos PDIR
│   └── commands/servidor/       # Comandos de servidor
├── docs/
│   ├── manuais/                 # Este manual
│   └── projeto/
│       ├── PRD.md               # Plano geral do projeto
│       ├── grupos/              # Arquivos de grupos (gerados)
│       ├── tarefas/             # Arquivos de tarefas (gerados)
│       └── .templates/          # Templates
└── src/                         # Código (após instalar Next.js)
```

---

## Dicas

- **Granularidade:** Uma tarefa = uma funcionalidade testável isoladamente
- **Commits:** Commite frequentemente, não espere terminar tudo
- **PRD primeiro:** Invista tempo no PRD antes de começar a dividir
- **Sequência:** Respeite dependências entre tarefas ao implementar

---

**Desenvolvido com Claude Code**
