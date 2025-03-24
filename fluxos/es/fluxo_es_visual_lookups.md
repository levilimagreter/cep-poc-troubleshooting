# Fluxo Visual – ES (Lookups, workflow actions e notificações)

```mermaid
graph TD
  A[Funcionalidades como lookup ou ação não funcionam no ES] --> B1[Lookup existe e está populado?]
  B1 -->|Não| F1[Revisar savedsearch de preenchimento e permissões do objeto]
  B1 -->|Sim| B2[Campos do lookup batem com os eventos esperados?]
  B2 -->|Não| F2[Corrigir campos de join ou SPL com `lookup` ou `inputlookup`]
  B2 -->|Sim| B3[Workflow action aparece nos notáveis?]
  B3 -->|Não| F3[Verificar se ação está habilitada para sourcetype/tags corretas]
  B3 -->|Sim| B4[Notificação (email, webhook) está funcionando?]
  B4 -->|Não| F4[Revisar alert actions e logs de envio de notificação]
  B4 -->|Sim| G[Funcionalidades de lookup e ação devem operar corretamente]
```

> 💡 Use este fluxo para investigar **por que lookups, ações customizadas ou notificações associadas a notáveis no ES não estão funcionando**.

