---
description: Para ambiente de desenvolvimento (Next.js + Prisma Studio)
---

# Dev Stop

Portas: ver `.env` ou `.env.example`

1. `fuser -k 3000/tcp` (Next.js)
2. `fuser -k 5555/tcp` (Prisma Studio)
3. Verificar: `lsof -i :3000 -i :5555 | grep LISTEN`

PostgreSQL (Docker) permanece rodando.
