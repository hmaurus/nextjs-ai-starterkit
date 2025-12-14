# Next.js AI Starterkit

Template para criar aplicativos fullstack Next.js com IA.

## Início Rápido

```bash
git clone git@github.com:hmaurus/nextjs-ai-starterkit.git seu-projeto
cd seu-projeto
/configurar-projeto
```

## Documentação

- **[Manual Completo](docs/manuais/starterkit-pdir.md)** - Starter kit + Método PDIR
- **[Regras do Projeto](CLAUDE.md)** - Stack, padrões, arquitetura

## Comandos

| Comando | Descrição |
|---------|-----------|
| `/configurar-projeto` | Wizard inicial (cria repo GitHub, .env) |
| `/dev-start` | Inicia Next.js + Prisma Studio |
| `/dev-stop` | Para serviços |
| `/planejar-e-implementar` | Tarefa avulsa (fora do PDIR) |

## Usar com Plugin PDIR (opcional)

Para o método PDIR completo, instale o plugin:

```bash
# No Claude Code
/plugin install pdir-workflow@hmaurus-masterclaude
```

Comandos disponíveis após instalação: `/pdir-criar-prd`, `/pdir-listar-grupos`, `/pdir-criar-issue`, etc.

---

> **Nota:** Este README será substituído pelo do seu projeto ao executar `/configurar-projeto`.
