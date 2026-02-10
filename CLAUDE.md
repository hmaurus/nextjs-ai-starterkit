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
**Config (raiz):** .env.example, .env.local, .eslintrc.json, .gitignore, .prettierrc, components.json, next.config.ts, package.json, pnpm-lock.yaml, postcss.config.mjs, README.md, tailwind.config.ts, tsconfig.json

## Dependências e Documentação

**Princípio:** Preferir versões latest stable + documentação atualizada como contexto.

1. Antes de usar qualquer API de lib externa, consultar Context7 MCP ou docs oficiais
2. Para libs de evolução rápida (Tailwind, Next.js, Prisma, React, shadcn/ui), SEMPRE verificar docs atuais
3. Se houver dúvida entre versões, perguntar ao usuário
4. Nunca assumir sintaxe/API sem verificação — o conhecimento do modelo pode estar desatualizado

## Ambiente e Configuração

**Obrigatórias:** `DATABASE_URL`, `DIRECT_URL`, `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_ANON_KEY`, `SUPABASE_SERVICE_ROLE_KEY`
**Opcionais:** `NEXT_PUBLIC_SITE_URL`, `APP_SLOT` (port offset: porta = base + SLOT × 10)
- `NEXT_PUBLIC_*` = exposto ao cliente; sem prefixo = servidor only
- Validar em `src/env.ts` via Zod; nunca commitar `.env` (apenas `.env.example`)

## DB workflow (database-first)

Supabase migrations = source of truth

1. `pnpm db:status`
2. Criar SQL em `supabase/migrations/`
3. `pnpm dlx supabase db reset`
4. `pnpm dlx prisma db pull`
5. `pnpm dlx prisma generate`

**PROIBIDO:** `prisma migrate dev`, `pnpm db:push`
**MANTER VAZIO:** `prisma/migrations/`

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

- Para gotchas de Next.js (RSC, async APIs, error handling, hydration): consultar skill `next-best-practices`
- Barrel imports: apenas via `@ui` (shadcn/ui); demais: imports diretos

## Erros

- Server Actions retornam `{ success: true, data } | { success: false, error }`
- Zod validation: `error.flatten().fieldErrors` para forms

## Regras de Implementação

- Princípios: KISS/YAGNI - simples, eficiente e claro; construir o que agrega valor imediato; evitar abstrações desnecessárias, over-engineering ou camadas extras
- Nomenclatura: Arquivos `kebab-case.ts`; Componentes `PascalCase`; funções/variáveis `camelCase`
- Docs: JSDoc obrigatório (props, uso, acessibilidade)
- Adicionar no cabeçalho de cada arquivo uma breve descrição do propósito e uso
- Usar dados reais do BD (sem mocks/stubs)
- Datas: strings ISO (YYYY-MM-DD) + `date-fns` para manipulação; BD usa `DATE`/`TIMESTAMPTZ`; UI BR → DD/MM/YYYY
- Após codificação: fazer checks, revisar requisitos e testar funcionalidades (ver seção Testes)

**Validação (Checks)**
- `pnpm check` → lint + prettier + typecheck
- `pnpm lint:fix` / `pnpm prettier:write` → correção automática

**Se algo der errado**
- Debugar profundamente antes de mudar de abordagem; nunca quebrar padrões definidos neste documento a menos que seja impossível

## Componentes

- Shadcn/UI: estender componentes base com CVA; usar blocos shadcn completos quando possível
- Imports: apenas via barrel `@ui` (`ui/index.ts`), nunca subpaths
- Registries: shadcn (336+) + extras (ex: @originui, @cult) via CLI (`pnpm dlx shadcn@latest add …`)

**Fluxo:** 1) Verificar se já existe em `ui/` → 2) Buscar no registry shadcn → 3) Buscar registries extras → 4) Perguntar ao usuário antes de customizar/criar do zero

**Customização:** DOM plano (≤3–4 níveis), utilitários mínimos, tags semânticas, sub-componentes menores

## Testes

**Princípios**
- Simular uso real, não internos isolados
- Dados variados e edge cases (evitar hard-coded)
- Questionar requisitos/testes que pareçam inconsistentes

**Execução**
- `pnpm test` → unit/integration | `pnpm test:e2e` → end-to-end | `pnpm test:coverage` (mínimo 80%)
- Pirâmide: priorizar unit → integration → E2E (proporção varia por contexto)

**Stack:** Vitest + React Testing Library (unit/integration) | Playwright via MCP para debug visual (E2E)

## Design (UX/UI)

- Estilo moderno: animações sutis (Framer Motion), sombras suaves, cantos arredondados; animações não devem quebrar fluxo
- ≤3 cliques para objetivos principais; carregamento <3s (Lighthouse >90)
- Acessibilidade: WCAG 2.2 AA, navegação por teclado, leitores de tela, aria, contraste
- Responsivo: mobile-first, alvos de toque 44px
- Feedback: estados de loading, erros claros, confirmações

## Segurança

- Se disponível, usar skill `security-guidance` para auditorias
- Validar todas as entradas com Zod; sanitizar outputs para prevenir XSS
- Proteger rotas via middleware; nunca logar/expor dados sensíveis
- Auth: Supabase Auth; middleware protege `/account/*`, `/admin/*`
- Verificar role em Server Actions via `supabase.auth.getUser()` → `user_metadata.role`
- Páginas de erro: `forbidden.tsx` (403), `unauthorized.tsx` (401)

## Git Workflow

- Commits: Conventional Commits (`feat(scope): msg`)

## Supabase

- **CLI** para operações comuns: `supabase db push`, `supabase functions deploy`, `supabase gen types typescript`, `supabase projects list`
- **API direta** para logs e advisors: consultar docs Supabase Management API (`api.supabase.com/v1/projects/`)

## Skills/ Plugins e Referências

- Para padrões Next.js/React detalhados: consultar skills `next-best-practices` e `vercel-react-best-practices`
- Antes de usar API de lib externa: consultar Context7 MCP ou docs oficiais
- Para tarefas de design UI/UX use o plugin/ skill `frontend-design`
- Usar skill `find-skills` para descobrir skills úteis para a atividade atual