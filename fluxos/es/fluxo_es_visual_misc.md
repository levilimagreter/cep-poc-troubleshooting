# Fluxo Visual – ES (Comportamentos diversos e anomalias)

```mermaid
graph TD
  A[Comportamentos inesperados no ES: risco, timelines, macros] --> B1[Score de risco não é gerado ou aparece 0?]
  B1 -->|Sim| F1[Verificar preenchimento do risk index e risk modifiers]
  B1 -->|Não| B2[Timeline de investigação não carrega notáveis?]
  B2 -->|Sim| F2[Verificar se `notable` tem campos exigidos: rule_name, time]
  B2 -->|Não| B3[Macro usada nos painéis retorna erro ou nada?]
  B3 -->|Sim| F3[Testar macro com `| makeresults` e revisar definição]
  B3 -->|Não| B4[Workflow action não aparece?]
  B4 -->|Sim| F4[Verificar tags associadas ao evento e escopo da ação]
  B4 -->|Não| G[Sistema deve funcionar normalmente após ajustes]
```

> 💡 Use este fluxo para diagnosticar **falhas pontuais no ES**, como ausência de risco, painéis incompletos ou macros quebradas.

