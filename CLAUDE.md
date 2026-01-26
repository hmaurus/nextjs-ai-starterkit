# Memória

## Visão Geral do Projeto

**Descrição**
Um Starterkit para criar aplicativos web full-stack em Next.js assistido por IA.

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

**src/components/**
ui/, shared/, admin/, account/, public/

**src/lib/**
actions/, queries/, services/, hooks/, i18n/, types/, validators/, config/, db.ts, utils.ts

**src/**
messages/{pt-BR,en}.json, middleware.ts, env.ts

**Config (raiz)**
.env.example, .env.local, .eslintrc.json, .gitignore, .prettierrc, components.json, next.config.ts, package.json, pnpm-lock.yaml, postcss.config.mjs, README.md, tailwind.config.ts, tsconfig.json

## Idioma

- Código em inglês (vars, funcs, classes, entidades); Comunicação com o usuário e documentação em português brasileiro (pt-BR)

## Dependências e Documentação

**Princípio:** Preferir versões latest stable + documentação atualizada como contexto.

**Workflow:**
  1. Antes de usar qualquer API de lib externa, consultar Context7 MCP ou docs oficiais
  2. Para libs de evolução rápida (Tailwind, Next.js, Prisma, React), SEMPRE verificar docs atuais
  3. Se houver dúvida entre versões, perguntar ao usuário
  4. Nunca assumir sintaxe/API sem verificação — o conhecimento do modelo pode estar desatualizado

**Libs que exigem atenção especial:**
- Next.js (App Router evolui rapidamente, v15→v16 teve mudanças significativas)
- Tailwind CSS (v3 → v4 teve breaking changes significativos)
- Prisma (v5 → v7 arquitetura completamente nova)
- shadcn/ui (componentes e CLI atualizados constantemente)

## Se algo der errado

- Debugar profundamente antes de mudar de abordagem; nunca quebrar padrões definidos neste documento a menos que seja impossível

## Stack

- Repositório com um único `package.json`
- Next.js 16+ (App Router com Server Components como padrão e TypeScript 5.9+)
- Tailwind CSS 4.1+ + Shadcn UI
- TanStack Query v5 (servidor) + Zustand 5 (cliente)
- Supabase/PostgreSQL + Prisma 7+ (ORM)
- pnpm (nunca npm/yarn)

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
- Server Actions: todas as mutações (CRUD) → `app/actions/*.ts` ou `'use server'`; formulários com RHF + Zod; operações diretas no BD via Prisma
- Estado: Local → Context → URL → Server → Global (escalar só se necessário)

**TypeScript**
- Strict mode: no implicit `any`, strict null checks
- Tipos de retorno: explícitos (exceto callbacks inline)
- Payloads de API: inferência via Zod (`z.infer<typeof schema>`)
- Evitar `any`: preferir `unknown` ou tipos específicos

## Regras de Implementação

- Princípios: KISS/YAGNI - simples, eficiente e claro; construir o que agrega valor imediato; evitar abstrações desnecessárias, over-engineering ou camadas extras
- Nomenclatura: kebab-case (arquivos), PascalCase (componentes), camelCase (vars/fns/prisma), UPPER_SNAKE_CASE (consts)
- Docs/Comentários: código auto-documentado; comentários inline para clareza; detalhados para lógica complexa
- File header: path + propósito
- Usar dados reais do BD (sem mocks/stubs)
- Datas: formulários → strings → `createDateOnly`; armazenar/manipular via `toDateString` (YYYY-MM-DD); exibir na UI BR → DD/MM/YYYY
- Após codificação: fazer checks, revisar requisitos e testar funcionalidades (ver seção Testes)

**Validação (Checks)**
- `pnpm check` → lint + prettier + typecheck
- `pnpm lint:fix` / `pnpm prettier:write` → correção automática

## Supabase

- **CLI** para operações comuns:
  `supabase db push`, `supabase functions deploy`, `supabase gen types typescript`, `supabase projects list`

- **API direta** para o que a CLI não cobre:

  ```bash
  # Logs (service: api|postgres|auth|storage|realtime|edge-function)
  curl -s "https://api.supabase.com/v1/projects/{project_id}/analytics/endpoints/logs.all?iso_timestamp_start=$(date -u -d '1 hour ago' +%Y-%m-%dT%H:%M:%SZ)" \
    -H "Authorization: Bearer $SUPABASE_ACCESS_TOKEN"

  # Advisors (type: security|performance)
  curl -s "https://api.supabase.com/v1/projects/{project_id}/advisors/{type}" \
    -H "Authorization: Bearer $SUPABASE_ACCESS_TOKEN"
  ```

## Componentes

- Shadcn/UI: estender componentes base com CVA; usar blocos shadcn completos quando possível
- Docs: JSDoc obrigatório (props, uso, acessibilidade)
- Imports: apenas via barrel `@ui` (`ui/index.ts`), nunca subpaths
- Registries: shadcn (336+) + extras (ex: @originui, @cult) via CLI (`pnpm dlx shadcn@latest add …`)

**Fluxo de uso de Componentes**
1. Verificar se já existe em `ui/`
2. Buscar no registry shadcn oficial
3. Se não encontrar, buscar em registries extras (@originui, @cult, etc.)
4. Se ainda nao encontrar, perguntar ao usuário se quer então customizar/criar do zero

**Se for Customizar:**
- Seguir guia Tailwind
- DOM plano (≤3–4 níveis), utilitários mínimos, tags semânticas
- Quebrar componentes complexos em sub-componentes menores

## Testes

**Princípios**
- Testar comportamento do usuário, não detalhes de implementação
- Simular uso real, não internos isolados
- Dados variados e edge cases (evitar hard-coded)
- Questionar requisitos/testes que pareçam inconsistentes

**Execução**
- `pnpm test` → unit/integration
- `pnpm test:e2e` → end-to-end
- Pirâmide: priorizar unit → integration → E2E (proporção varia por contexto)

**Stack**
- Unit/Integration: Vitest + React Testing Library
- E2E: Playwright (via MCP para debug visual)
- Cobertura: `pnpm test:coverage` (mínimo 80%)

## Design (UX/UI)

**Estilo**
- Toque moderno, exemplo: animações sutis (Framer Motion), sombras suaves, cantos arredondados, cards com Animated Gradient Glow Border, glassmorphism.
- Animações não devem quebrar fluxo principal

**Obrigatórios**
- ≤3 cliques para objetivos principais
- Carregamento <3s (Lighthouse >90) com estados apropriados
- Acessibilidade: WCAG 2.2 AA, navegação por teclado, leitores de tela, aria, contraste
- Responsivo: mobile-first, alvos de toque de 44px
- Feedback: estados de loading, erros claros, confirmações

## Segurança

- Validar todas as entradas com **Zod**
- Proteger rotas via middleware
- Nunca logar/expor dados sensíveis
- Sanitizar outputs para prevenir XSS

## Recursos

**Usuários de Teste**
Formato: `Nome: [email, senha, roles...]`
- admin: `[admin@example.com, Senha@123, ADMIN]`
- manager: `[manager@example.com, Senha@123, MANAGER]`
- user: `[user@example.com, Senha@123, USER]`

**Papéis (ENUM):** ADMIN, MANAGER, USER
