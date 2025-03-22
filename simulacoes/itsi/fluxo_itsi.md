# Fluxo de Troubleshooting – Splunk ITSI (IT Service Intelligence)

Este fluxo orienta o diagnóstico de problemas comuns no **Splunk ITSI**, com base nos 30 cenários simulados. A estrutura combina **árvore decisória + pensamento técnico**, facilitando a navegação por falhas em KPIs, serviços, notáveis, glass tables, EPMs e outros.

---

## 📉 Um ou mais **KPIs estão nulos, congelados ou estranhos**?

➡️ **KPI retorna NULL ou nenhum dado?** → [01_kpi_nulo.md]  
➡️ **KPI tem sempre o mesmo valor (flat)?** → [02_kpi_com_valores_constantes.md]  
➡️ **KPI não está agendado?** → [03_kpi_sem_agendamento.md]  
➡️ **KPI não atualiza (sem coleta)?** → [04_kpi_nao_atualiza.md]  
➡️ **Base Search do KPI está ausente ou inválida?** → [05_kpi_sem_searchbase.md]  
➡️ **Erro de sintaxe na search do KPI?** → [06_kpi_com_sintaxe_errada.md]

### 🧠 Como pensar:
1. A base search do KPI está ativa, agendada e com resultados?
2. O SPL usado retorna valor numérico?
3. Verifique `_internal` e `itsi_summary` para ver falhas de execução
4. O KPI foi clonado e perdeu dependências?

---

## 🧭 O KPI **não está vinculado corretamente ao serviço**?

➡️ **KPI não aparece vinculado a nenhum serviço?** → [07_kpi_nao_associado_a_servico.md]  
➡️ **Granularidade de coleta está errada (ex: 24h ao invés de 5m)?** → [08_kpi_com_granularidade_errada.md]  
➡️ **KPI aparece no serviço mas não aparece na Glass Table?** → [09_kpi_nao_exibe_em_glass_table.md]

### 🧠 Como pensar:
1. O KPI está associado via template ou manualmente?
2. O intervalo de coleta (`interval`) condiz com o tipo de métrica?
3. A Glass Table está usando corretamente o token ou variável esperada?

---

## 🏗️ O **serviço está com estrutura inconsistente**?

➡️ **Serviço não tem dependências (vazio)?** → [10_servico_sem_dependencias.md]  
➡️ **Serviço não aparece na árvore de serviços?** → [11_servico_nao_aparece_arvore.md]  
➡️ **Serviço não gera notável mesmo com degradação?** → [12_servico_nao_gera_notavel.md]  
➡️ **Nome do serviço conflita com outro já existente?** → [13_servico_com_nome_duplicado.md]  
➡️ **Agregação do KPI está errada (ex: avg vs. latest)?** → [14_agregacao_incorreta_kpi.md]

### 🧠 Como pensar:
1. O serviço está com status "Enabled"?
2. A árvore de dependências foi validada no editor visual?
3. Os thresholds estão configurados corretamente?
4. A ação de alerta está habilitada e com permissões?

---

## 🧪 O comportamento do KPI/serviço **não dispara como esperado**?

➡️ **KPI não exibe detalhamento (drilldown)?** → [15_kpi_sem_detalhamento.md]  
➡️ **ITMV não associa anomalias corretamente?** → [16_servico_sem_correlacao_itmv.md]  
➡️ **KPIs de infraestrutura não reagem a quedas?** → [17_kpi_de_infra_nao_reage.md]  
➡️ **Umbral não foi configurado corretamente?** → [18_kpi_sem_umbral_configurado.md]  
➡️ **KPI foi criado mas está desativado?** → [19_kpi_configurado_mas_desativado.md]

### 🧠 Como pensar:
1. O KPI tem thresholds de alerta e crítico?
2. Há script de correlação ativo no ITMV?
3. Há dependências condicionais no serviço?
4. Os dados estão com `_time` correto?

---

## 🔗 Problemas com **referências, Glass Tables e EPM**?

➡️ **Serviço com referência quebrada ou inválida?** → [20_servico_com_referencia_invalida.md]  
➡️ **Glass Table não exibe os KPIs esperados?** → [21_glass_table_vazia.md]  
➡️ **Erro visual ou token quebrado na Glass Table?** → [22_glass_table_com_erro_visual.md]  
➡️ **Anomalias não aparecem no painel de anomalias?** → [23_anomalias_nao_detectadas.md]  
➡️ **ITMV com erro de coleta ou sem eventos?** → [24_itmv_quebrado.md]  
➡️ **EPM sem preencher episódios corretamente?** → [25_epm_nao_popula.md]

### 🧠 Como pensar:
1. Validar os componentes da Glass Table (tokens, métricas, KPIs)
2. O ITMV está habilitado e com searches agendadas?
3. O EPM está ativo? Há eventos nos índices `itsi_tracked_alerts` e `itsi_summary`?

---

## 🚨 Notáveis, correlações e dependências quebradas?

➡️ **Notáveis não aparecem mesmo com degradação?** → [26_notaveis_sem_episodios.md]  
➡️ **Correlações entre KPIs não funcionam?** → [27_correlacao_entre_kpis_falha.md]  
➡️ **Serviço pai não reage à degradação do filho?** → [28_servico_nao_reage_a_dependente.md]  
➡️ **KPI com SPL filtrando demais os eventos?** → [29_kpi_com_filtro_excessivo.md]  
➡️ **Serviço sem histórico na timeline?** → [30_servico_nao_tem_historico.md]

### 🧠 Como pensar:
1. Verificar se a action rule do serviço está habilitada
2. O episódio é gerado no EPM e referenciado corretamente?
3. O SPL do KPI contém `where`, `eval` ou `rex` que filtra tudo?
4. O `itsi_summary` tem eventos para o serviço afetado?

---

> 💡 Dica final: Use os painéis de Health Check e o `_internal`, `_audit` e `itsi_summary` para investigar comportamentos de execução, agendamento e coleta dentro do ITSI.
