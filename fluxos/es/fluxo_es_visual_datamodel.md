# Fluxo Visual – ES (Modelos de dados e normalização)

```mermaid
graph TD
  A[Painéis do ES não preenchem com base nos modelos de dados] --> B1[Modelo de dados está acelerado?]
  B1 -->|Não| F1[Habilitar aceleração do datamodel necessário]
  B1 -->|Sim| B2[Pivot do datamodel retorna dados?]
  B2 -->|Não| F2[Testar pivot ou usar `| datamodel` para validação]
  B2 -->|Sim| B3[Eventos têm tags esperadas aplicadas?]
  B3 -->|Não| F3[Revisar TAG-* no props.conf ou falta de eventtype]
  B3 -->|Sim| B4[Campos esperados (user, src, etc) foram extraídos?]
  B4 -->|Não| F4[Normalizar com FIELDALIAS, EXTRACT ou lookups automáticos]
  B4 -->|Sim| G[Modelo de dados deve funcionar corretamente]
```

> 💡 Use este fluxo quando **modelos de dados no ES parecem vazios ou incompletos**, mesmo com eventos visíveis no Search.

