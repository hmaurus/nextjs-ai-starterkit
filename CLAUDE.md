# AGENT

**IMPORTANTE:** Template vazio — sem `src/`, `prisma/`, `supabase/` até setup inicial.
Executar `/configurar-projeto` para inicializar. Criar estrutura sob demanda conforme seção "Estrutura de Diretórios".

## Visão Geral do Projeto

**Descrição**
Um Starterkit para criar aplicativos web full-stack em Next.js assistido por IA.

## Idioma

- Código em inglês (vars, funcs, classes, entidades); Comunicação com o usuário e documentação em pt-BR

## Stack

- Repositório com um único `package.json`
- Next.js 16+ (App Router, Server Components como padrão, TypeScript 5.9+ Strict)
- Tailwind CSS 4+ + Shadcn UI
- React Hook Form + Zod (validação de forms e payloads)
- TanStack Query v5 (client-side cache, polling, optimistic updates) + Zustand 5 (estado global cliente)
- Supabase/PostgreSQL + Prisma 7+ (ORM) + date-fns (datas)
- pnpm

## Estrutura de Diretórios sugerida

**Raiz**
prisma/schema.prisma, supabase/{migrations,seed.sql,config.toml}, public/{favicon.ico,images/}, e2e/example.spec.ts

**src/app/**
- [locale]/(public)/{page,login,register,forgot-password,[slug],layout}.tsx
- [locale]/(account)/account/{page,settings,profile,layout}.tsx
- [locale]/(admin)/admin/{page,users,settings,layout}.tsx
- [locale]/layout.tsx
- api/{webhooks,ai}/route.ts
- layout.tsx, globals.css, not-found.tsx, error.tsx, sitemap.ts, robots.ts

**src/components/** ui/, shared/, admin/, account/, public/
**src/lib/** actions/, queries/, services/, hooks/, i18n/, types/, validators/, config/, db.ts, utils.ts
**src/** messages/{pt-BR,en}.json, middleware.ts, env.ts
**Config (raiz):** .env.example, .env.local, eslint.config.mjs, .gitignore, .prettierrc, components.json, next.config.ts, package.json, pnpm-lock.yaml, README.md, tsconfig.json

## Dependências e Documentação

**Princípio:** Preferir versões latest stable + documentação atualizada como contexto.

1. Antes de usar qualquer API de lib externa, consultar Context7 MCP ou docs oficiais
2. Para libs de evolução rápida (Tailwind, Next.js, Prisma, React, shadcn/ui), SEMPRE verificar docs atuais
3. Se houver dúvida entre versões, perguntar ao usuário

## Ambiente e Configuração

**Obrigatórias:** `DATABASE_URL`, `DIRECT_URL`, `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_ANON_KEY`, `SUPABASE_SERVICE_ROLE_KEY`
**Opcionais:** `NEXT_PUBLIC_SITE_URL`, `APP_SLOT`
- `APP_SLOT` define offset de portas: `porta = base + SLOT × 10` — ao mudar SLOT, ajustar portas no `.env` e no `package.json` (dev/start)
- Bases: Next=3000, Prisma Studio=5555, Supabase API=54321, DB=54322, Studio=54323, Inbucket=54324
- `NEXT_PUBLIC_*` = exposto ao cliente; sem prefixo = servidor only
- Validar em `src/env.ts` via Zod; nunca commitar `.env` (apenas `.env.example`)

## DB workflow (database-first)

Supabase migrations = source of truth

1. `supabase db diff`
2. Criar SQL em `supabase/migrations/`
3. `supabase db reset`
4. `prisma db pull`
5. `prisma generate`

**PROIBIDO:** `prisma migrate dev`, `prisma db push`
**Nota:** `supabase db push` (deploy de migrations para remoto) é permitido — não confundir com Prisma
**MANTER VAZIO:** `prisma/migrations/`
- Supabase CLI e Management API (`api.supabase.com/v1/projects/`) disponíveis — consultar docs quando necessário

## Padrões de Código

**Arquitetura**
- Lógica de negócio: centralizar em `lib/` (actions, queries)
- Dados: usar Prisma diretamente em Server Components (sem abstrações)
- Forms: apenas Server Actions (nunca fetch manual)
- Operações multi-step: usar transactions

**Fluxo de dados**
- Server Components: todas as páginas/exibição de dados → `app/*/page.tsx`, `app/*/layout.tsx`
- Server Actions: todas as mutações (CRUD) → `lib/actions/*.ts` ou `'use server'`; operações diretas no BD via Prisma
- TanStack Query v5: client-side cache (polling, optimistic updates) — não substitui Server Components para data fetching
- Estado: Local → Context → URL → Server → Global (escalar só se necessário)

**TypeScript**
- Tipos de retorno: explícitos (exceto callbacks inline)
- Payloads de API: inferência via Zod (`z.infer<typeof schema>`)

## Gotchas

- Para gotchas de Next.js (RSC, async APIs, error handling, hydration): consultar skill (se disponível) `next-best-practices`

## Erros

- Server Actions retornam `{ success: true, data } | { success: false, error }`
- Zod validation: `error.flatten().fieldErrors` para forms

## Regras de Implementação

- Princípios: KISS/YAGNI - evitar abstrações desnecessárias e over-engineering
- Nomenclatura: Arquivos `kebab-case.ts`; Componentes `PascalCase`; funções/variáveis `camelCase`
- Docs: Adicione JSDoc (descrição, @param, @returns) em toda função exportada que você criar ou modificar.
- Adicionar no cabeçalho de cada arquivo uma breve descrição do propósito e uso
- Datas: strings ISO (YYYY-MM-DD) + `date-fns` para manipulação; BD usa `DATE`/`TIMESTAMPTZ`; UI BR → DD/MM/YYYY

**Validação (Checks)**
- `eslint .` + `prettier --check .` + `tsc --noEmit`
- Fix: `eslint . --fix` / `prettier --write .`

## Componentes

- Shadcn/UI: estender componentes base com CVA; usar blocos shadcn completos quando possível
- Imports: apenas via barrel `@ui` (`ui/index.ts`), nunca subpaths
- Registries: shadcn (336+) + extras (ex: @originui, @cult) via CLI (`pnpm dlx shadcn@latest add …`)

**Fluxo:** 1) Verificar se já existe em `ui/` → 2) Buscar no registry shadcn → 3) Buscar registries extras → 4) Perguntar ao usuário antes de customizar/criar do zero

**Customização:** DOM plano (≤3–4 níveis), tags semânticas, composição via sub-componentes

## Testes

- `vitest run` → unit/integration | `playwright test` → E2E | `vitest run --coverage` (mínimo 80%)
- Stack: Vitest + React Testing Library (unit/integration) | Playwright via MCP para debug visual (E2E)

## Design (UX/UI)

- Animações: Framer Motion (sutis, não bloquear fluxo)
- Performance: Lighthouse >90
- Acessibilidade: WCAG 2.2 AA
- Responsivo: mobile-first, alvos de toque 44px

## Segurança

- Auth: Supabase Auth; middleware protege `/account/*`, `/admin/*`
- Verificar role em Server Actions via `supabase.auth.getUser()` → `user_metadata.role`
- Páginas de erro: `forbidden.tsx` (403), `unauthorized.tsx` (401)

## Skills e Plugins (se disponíveis)

- Para padrões Next.js/React detalhados: consultar skills `next-best-practices` e `vercel-react-best-practices`
- Para tarefas de design UI/UX use o plugin/ skill `frontend-design`
- Usar skill `find-skills` para descobrir skills úteis para a atividade atual