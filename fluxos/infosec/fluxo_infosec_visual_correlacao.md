# Fluxo Visual – Infosec (Falta de correlação ou campos esperados)

```mermaid
graph TD
  A[Eventos presentes mas painel de correlação vazio] --> B1[Eventtype aplicado aos eventos?]
  B1 -->|Não| F1[Revisar definição de eventtype, sourcetype e tags]
  B1 -->|Sim| B2[Tags esperadas estão aplicadas (ex: attack, authentication)?]
  B2 -->|Não| F2[Configurar tags via props.conf e TAG-*]
  B2 -->|Sim| B3[Modelo de dados populado corretamente?]
  B3 -->|Não| F3[Habilitar aceleração e validar pivots do datamodel]
  B3 -->|Sim| B4[Campos esperados (user, src, action) existem?]
  B4 -->|Não| F4[Normalizar via EXTRACT/REPORT ou aliases]
  B4 -->|Sim| G[Correlações devem aparecer nos painéis do app]
```

> 💡 Este fluxo cobre o cenário onde há dados, mas **nenhuma correlação aparece** nos painéis do Infosec — por falta de tags, campos esperados ou mapeamento no datamodel.
