# Fluxo Visual – Infosec (Busca sem retorno ou com erro)

```mermaid
graph TD
  A[Busca nos painéis ou alerts não retorna eventos] --> B1[Filtro de tempo está correto?]
  B1 -->|Não| F1[Ajustar timepicker ou SPL com earliest/latest]
  B1 -->|Sim| B2[Index e sourcetype usados estão corretos?]
  B2 -->|Não| F2[Revisar macro ou SPL manualmente]
  B2 -->|Sim| B3[Campos usados na busca existem nos eventos?]
  B3 -->|Não| F3[Corrigir `where`, `stats`, ou `eval` quebrado]
  B3 -->|Sim| B4[Macro tem erro de sintaxe ou campo inexistente?]
  B4 -->|Sim| F4[Testar macro com `| makeresults` ou `| `macro``]
  B4 -->|Não| B5[Savedsearch ou painel usa busca com token?]
  B5 -->|Sim| F5[Verificar valor padrão e escopo dos tokens]
  B5 -->|Não| G[Busca deve retornar eventos normalmente]
```

> 💡 Use este fluxo quando **buscas do Infosec** (em painéis, macros ou alertas) **não retornam eventos mesmo com dados ingeridos**.
