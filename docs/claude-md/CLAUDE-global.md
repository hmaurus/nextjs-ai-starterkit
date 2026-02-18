# AGENT — Regras Globais (~/.claude/CLAUDE.md)

## Idioma

- Código em inglês (vars, funcs, classes, entidades); Comunicação com o usuário e documentação em pt-BR (commits, gitgub issues, mensagens de erro, comentários etc)

## Dependências e Documentação

**Princípio:** Preferir versões latest stable + documentação atualizada como contexto.

1. Antes de usar qualquer API de lib externa, consultar Context7 MCP ou docs oficiais
2. Para libs de evolução rápida, SEMPRE verificar docs atuais
3. Se houver dúvida entre versões, perguntar ao usuário

## Regras de Implementação

- Princípios: KISS/YAGNI — evitar abstrações desnecessárias e over-engineering
- Nomenclatura: Arquivos `kebab-case.ts`; Componentes `PascalCase`; funções/variáveis `camelCase`
- Docs: Adicione JSDoc (descrição, @param, @returns) em toda função exportada que você criar ou modificar
- Adicionar no cabeçalho de cada arquivo uma breve descrição do propósito e uso

## Validação (Checks)

- `eslint .` + `prettier --check .` + `tsc --noEmit`
- Fix: `eslint . --fix` / `prettier --write .`

## Erros

- Server Actions retornam `{ success: true, data } | { success: false, error }`
- Zod validation: `error.flatten().fieldErrors` para forms

## Datas

- Strings ISO (YYYY-MM-DD) + lib de datas para manipulação; BD usa `DATE`/`TIMESTAMPTZ`; UI BR → DD/MM/YYYY

## Testes

- Unit/integration primeiro, E2E para fluxos críticos
- Cobertura mínima: 80%

## Design (UX/UI)

- Performance: Lighthouse >90
- Acessibilidade: WCAG 2.2 AA
- Responsivo: mobile-first, alvos de toque 44px
- Animações sutis, não bloquear fluxo

## Segurança

- Nunca commitar `.env` (apenas `.env.example`)
- `NEXT_PUBLIC_*` = exposto ao cliente; sem prefixo = servidor only
- Validar variáveis de ambiente via Zod
- Páginas de erro: `forbidden.tsx` (403), `unauthorized.tsx` (401)
