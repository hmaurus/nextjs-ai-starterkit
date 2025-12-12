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
/pdir:pdir-criar-issue [descrição]      # Cria Issue
/pdir:pdir-implementar-tarefa [issue]   # Implementa
/pdir:pdir-commit                       # Commit + push
/pdir:pdir-draft-pr                     # Cria Draft PR
/pdir:pdir-ready-pr [pr]                # Marca PR pronto
/pdir:pdir-merge-tarefa [pr]            # Merge + limpeza
```

## Testes

```bash
pnpm test        # unit/integration
pnpm test:e2e    # end-to-end
```

---

*Desenvolvido com Claude Code e Método PDIR*
