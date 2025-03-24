# Fluxo Visual – ITSI (Notáveis, correlações e dependências quebradas)

```mermaid
graph TD
  A[Serviço ou KPI não gera notável ou correlação] --> B1[Serviço tem action rule habilitada?]
  B1 -->|Não| F1[Habilitar e configurar regra de ação no serviço]
  B1 -->|Sim| B2[Episódio aparece no EPM?]
  B2 -->|Não| F2[Validar execução de EPM e status do ITSI Notable Engine]
  B2 -->|Sim| B3[Correlator Search está ativa? (ITMV)]
  B3 -->|Não| F3[Ativar e configurar busca de correlação no ITMV]
  B3 -->|Sim| B4[Eventos chegam no índice itsi_tracked_alerts?]
  B4 -->|Não| F4[Verificar permissões, roteamento e index correto]
  B4 -->|Sim| B5[Dependência entre serviços está configurada?]
  B5 -->|Não| F5[Adicionar dependências na árvore de serviços]
  B5 -->|Sim| G[Notáveis e correlações devem funcionar corretamente]
```

> 💡 Use este fluxo quando **KPIs ou serviços não geram alertas, episódios ou correlações**, mesmo diante de degradação evidente.
