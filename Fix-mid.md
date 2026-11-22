---
description: Corrige bugs ou problemas de escopo médio mantendo arquitetura e contratos existentes.
---

Analise e corrija o problema reportado seguindo estritamente estas diretrizes:

1. **Diagnóstico:** Identifique a causa raiz antes de alterar qualquer código.
2. **Escopo Controlado:** Corrija o bug e efeitos colaterais diretos sem refatorações desnecessárias fora do contexto.
3. **Código Limpo:** Mantenha tipagem estrita, nomes claros, funções coesas e siga os padrões já adotados no projeto.
4. **Sem Regressões:** Preserve assinaturas públicas e contratos de API existentes.
5. **Validação:** Liste os arquivos alterados e sugira ou atualize os testes unitários/integração correspondentes.

Problema a resolver: $ARGUMENTS