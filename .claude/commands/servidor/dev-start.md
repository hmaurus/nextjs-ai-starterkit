---
description: Inicia ambiente de desenvolvimento (Next.js + Prisma Studio)
---

# Dev Start

Portas: ver `.env` ou `.env.example`

1. Verificar portas livres: `lsof -i :3000 -i :5555 | grep LISTEN`
2. Se ocupadas: `fuser -k PORTA/tcp`
3. `pnpm dev` (background)
4. `pnpm prisma studio --port 5555` (background)
5. Monitorar com BashOutput

### Feedback Final
URLs do App e Prisma Studio iniciados.
