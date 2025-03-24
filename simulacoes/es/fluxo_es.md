# Fluxo de Troubleshooting – Splunk ES (Enterprise Security)

Este fluxo cobre os principais problemas no uso do **Splunk Enterprise Security (ES)** com base nas 30 simulações desenvolvidas. Use como árvore de decisão + raciocínio técnico para investigação estruturada.

---

## 🚨 Os **notáveis sumiram** ou **não estão sendo gerados**?

➡️ **Nenhum notável visível no painel?** → [01_notaveis_sumiram.md]  
➡️ **Correlation Search não está agendando?** → [02_correlation_nao_agenda.md]  
➡️ **Não há dados suficientes para disparar a regra?** → [03_falta_dados_para_correlation.md]  
➡️ **Erro em lookups referenciados pela regra?** → [04_lookup_nao_encontrado.md]

### 🧠 Como pensar:
1. As regras estão habilitadas? (Settings → Correlation Searches)
2. O agendamento está ativo e executando? (ver `scheduler.log`)
3. Os campos esperados (`src`, `user`, `signature`) estão presentes?
4. O `index` usado pela busca retorna eventos no período esperado?
5. O lookup usado na correlação existe e está populado?

---

## 🧩 Os **notáveis estão sendo gerados**, mas **com problemas no conteúdo**?

➡️ **Notáveis sem campos de contexto (dispositivo, usuário)?** → [05_notaveis_sem_detalhe.md], [09_notaveis_sem_dispositivo.md]  
➡️ **Notável com valores incorretos ou inconsistentes?** → [06_notaveis_com_dados_errados.md]  
➡️ **Correlation Search com SPL inválido?** → [07_correlation_com_sintaxe_invalida.md]  
➡️ **Prioridade dos notáveis está inconsistente?** → [26_conflito_de_prioridade.md]  
➡️ **Alertas gerados são muito genéricos ou específicos demais?** → [17_notaveis_muito_genericos.md], [18_notaveis_muito_especificos.md]

### 🧠 Como pensar:
1. A regra de correlação retorna os campos esperados?
2. Há campos `notable_owner`, `urgency`, `risk_score` definidos?
3. O macro usado aplica filtros excessivos?
4. As tags ou eventtypes esperados estão nos eventos?

---

## ⚙️ As **rules estão configuradas**, mas **não disparam**?

➡️ **Agendamento incorreto ou muito espaçado?** → [02_correlation_nao_agenda.md]  
➡️ **Erro ao rodar a savedsearch (problema de permissão)?** → [08_escalonamento_nao_dispara.md]  
➡️ **Problema com tags esperadas na regra?** → [10_falta_tag_esperada.md]  
➡️ **Problema no domínio (`domain`/`threat`) resolvido incorretamente?** → [11_dominio_nao_resolvido.md]  
➡️ **Dados chegam atrasados (delay de indexação)?** → [27_dados_chegam_com_delay.md]

### 🧠 Como pensar:
1. Regras estão habilitadas?
2. O usuário tem permissões para `run_alert`, `schedule_search`?
3. `risk_notable_index` ou `notable` existe e está disponível?
4. SPL funciona fora da savedsearch?
5. Tem delay entre `_time` e `_indextime` dos eventos?

---

## 🔎 Os **painéis do ES não mostram os notáveis corretamente**?

➡️ **Painel principal sem dados?** → [12_notaveis_nao_aparecem_dashboard.md]  
➡️ **Timeline dos notáveis está vazia ou desatualizada?** → [13_timeline_nao_atualiza.md]  
➡️ **Eventos de risco (risk) não aparecem para usuários?** → [14_risco_usuarios_sem_correlacao.md]  
➡️ **Notáveis não aparecem na Investigação?** → [15_nao_aparece_em_investigacao.md]

### 🧠 Como pensar:
1. Painéis do ES usam macros e base searches específicas
2. O `notable` tem campos como `urgency`, `security_domain`, `src`, `dest`?
3. A role do usuário tem acesso ao domínio de segurança do notável?
4. Risk rules estão populando índices corretos?

---

## 🔁 Problemas com **modelos de dados e normalização**?

➡️ **Modelo de dados (`datamodel`) está vazio?** → [16_modelo_dados_nao_preenche.md]  
➡️ **Eventtypes e tags não estão aplicados corretamente?** → [19_eventtypes_errados.md], [25_tags_mal_configuradas.md]  
➡️ **Score de correlação não está sendo gerado?** → [21_correlation_sem_score.md]

### 🧠 Como pensar:
1. Os datamodels foram acelerados? (`Settings > Data Models`)
2. Há dados com tags esperadas como `authentication`, `attack`?
3. O sourcetype está mapeado em `props.conf` com TAGS?

---

## 🧷 Problemas com **lookups ou ações complementares**?

➡️ **Lookup não existe ou está mal referenciado?** → [04_lookup_nao_encontrado.md], [20_lookup_desatualizado.md]  
➡️ **Ação de escalonamento (email, ticket) não funciona?** → [22_escalonamento_com_email_invalido.md]  
➡️ **Workflow action não executa corretamente?** → [28_workflow_action_quebrado.md]  
➡️ **Alerta/investigação não linka como deveria?** → [29_alerta_investigacao_nao_linka.md]

### 🧠 Como pensar:
1. O lookup foi preenchido por savedsearch?
2. Há permissão para leitura/escrita do lookup?
3. A ação de notificação está configurada corretamente no `alert_actions.conf`?
4. A workflow action usa tokens válidos?

---

## 🧨 Outros problemas no comportamento dos notáveis?

➡️ **Notável é gerado mas não alimenta risco?** → [30_nao_gera_risco_esperado.md]  
➡️ **Filtro aplicado em correlação exclui eventos?** → [24_correlation_nao_reage_a_filtro.md]

### 🧠 Como pensar:
1. O campo `risk_score` ou `risk_object` está sendo alimentado?
2. Os filtros aplicados na macro estão corretos?
3. A correlação está em modo `verbose` para debug?

---

> 💡 Dica: Sempre inicie validando se a busca retorna resultados fora do contexto da savedsearch. Use `_audit`, `_internal` e `| rest /services/saved/searches` para entender a execução real das regras e alertas no ES.

