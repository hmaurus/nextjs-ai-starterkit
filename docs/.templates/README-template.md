# [NOME_PROJETO]

[DESCRIÇÃO]

## Stack

- Next.js 15+ (App Router) + TypeScript 5+
- Tailwind CSS 4.1+ + shadcn/ui
- TanStack Query v5 + Zustand
- Supabase/PostgreSQL + Prisma 5+

## Setup

```bash
pnpm create next-app@latest . --typescript --tailwind --app
cp .env.example .env
docker compose up -d postgres
```

## Desenvolvimento

```bash
/dev-start    # Inicia Next.js + Prisma Studio
/dev-stop     # Para serviços
```

Portas: ver `.env.example`

## Workflow PDIR

```bash
/pdir-dividir-em-tarefas docs/projeto/PRD.md   # Divide PRD em tarefas
/pdir-criar-issue [descrição ou arquivo#seção] # Cria Issue no GitHub
/pdir-implementar-tarefa-com-branch [issue]    # Implementa com branch
/pdir-commit                                   # Commit + push
/pdir-criar-pr                                 # Cria PR vinculado a Issue
/pdir-merge-tarefa                             # Merge + limpeza
```

## Testes

```bash
pnpm test        # unit/integration
pnpm test:e2e    # end-to-end
```

---

*Desenvolvido com Claude Code e Método PDIR*
