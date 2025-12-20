---
description: Analisa, planeja e implementa uma tarefa avulsa (fora do workflow PDIR)
argument-hint: [descrição, @arquivo, imagem ou issue do GitHub]
---

# Planejar e Implementar

- Analisar o projeto atual
- Verificar a solicitação do usuário
- Planejar e Implementar a tarefa solicitada

**Entrada:** `$ARGUMENTS` - texto, arquivo (`@path`), imagem, github issue ou combinações

## Exemplos

```bash
/planejar-e-implementar "adicionar botão de logout no header"
/planejar-e-implementar @docs/requisitos/login.md
/planejar-e-implementar "criar formulário" @mockup.png
```

## Checklist

- [ ] TypeScript sem erros (`pnpm tsc`)
- [ ] Linting passou (`pnpm lint`)
- [ ] Funcionalidade implementada conforme solicitado
- [ ] Código segue padrões do projeto

### Feedback Final

```
✅ Implementação concluída!

📁 Arquivos modificados/criados:
- [lista]

Próximo passo: /pdir:pdir-commit
```
