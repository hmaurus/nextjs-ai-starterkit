---
description: Reinicia ambiente de desenvolvimento (Next.js + Prisma Studio)
---

# Dev Restart

Portas: ver `.env` ou `.env.example`

1. `fuser -k 3000/tcp && fuser -k 5555/tcp`
2. `sleep 3`
3. `pnpm dev` (background)
4. `pnpm prisma studio --port 5555` (background)
5. Monitorar com BashOutput

### Feedback Final
URLs do App e Prisma Studio reiniciados.
