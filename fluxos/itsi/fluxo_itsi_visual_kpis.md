# Fluxo Visual – ITSI (KPIs nulos, constantes ou com erro)

```mermaid
graph TD
  A[KPI não exibe dados ou mostra valor constante] --> B1[KPI está habilitado?]
  B1 -->|Não| F1[Habilitar KPI manualmente ou via template]
  B1 -->|Sim| B2[Base search está definida?]
  B2 -->|Não| F2[Associar base search ao KPI]
  B2 -->|Sim| B3[Base search retorna eventos?]
  B3 -->|Não| F3[Testar SPL e filtros de tempo ou index]
  B3 -->|Sim| B4[Campo extraído retorna valor numérico?]
  B4 -->|Não| F4[Corrigir SPL ou usar `eval` para conversão]
  B4 -->|Sim| B5[KPI tem intervalo de coleta compatível?]
  B5 -->|Não| F5[Ajustar granularidade (ex: 5m, 1m)]
  B5 -->|Sim| B6[Thresholds estão configurados?]
  B6 -->|Não| F6[Definir warning/critical thresholds no KPI]
  B6 -->|Sim| G[KPI deve exibir dados corretamente no painel ou serviço]
```

> 💡 Use este fluxo quando um **KPI do ITSI está sempre nulo, flat (constante) ou aparentemente quebrado**.
