# Fluxo de Troubleshooting – Splunk Core (Árvore Decisória + Raciocínio Técnico)

Este fluxo ajuda a identificar e resolver problemas comuns no Splunk Core com base nos 30 cenários simulados. A cada etapa, siga também o raciocínio técnico sugerido para aprofundar a investigação.

---

## 🔍 Os dados **não estão aparecendo** no index?

➡️ **Index inexistente ou incorreto?** → [01_index_vazio.md]  
➡️ **Erro de timestamp (evento fora do timerange)?** → [02_timestamp_incorreto.md]  
➡️ **Input está configurado mas não lê o arquivo?** → [04_input_sem_ingestao.md]  
  ↳ **Arquivo com permissão Linux incorreta?** → [20_permissao_arquivo_input.md]  
➡️ **Arquivo compactado `.gz` não é ingerido?** → [28_arquivo_gz.md]  
➡️ **Dados atrasados por timezone errado?** → [27_timezone_problems.md]  
➡️ **Problema de licença?** → [10_license.md]

### 🧠 Como pensar:
1. Forwarder configurado?
   └── Não → Corrigir instalação/configuração
   └── Sim ↓
2. Forwarder envia eventos?
   └── Ver `splunkd.log`, `outputs.conf`
3. Dados chegam no indexer?
   └── Verificar `_internal` e `metrics.log`
4. Index correto?
   └── Validar `index` em `inputs.conf`
5. Timestamp correto?
   └── Validar `_time`, `TIME_FORMAT`, etc.
6. Busca correta?
   └── Janela de tempo, sourcetype, permissions

---

## ⚠️ Os dados estão sendo ingeridos, mas **não aparecem corretamente**?

➡️ **Sourcetype não está sendo aplicado?** → [13_dados_sem_sourcetype.md]  
➡️ **Campos não estão sendo extraídos?** → [12_extracao_incorreta.md]  
➡️ **Timestamp ausente?** → [15_timestamp_ausente.md]  
➡️ **Eventos unidos ou quebrados?** → [29_linebreaker.md]  
➡️ **Codificação do arquivo é inválida?** → [23_file_codificacao_errada.md]  
➡️ **Campo `host` ou `source` inesperado?** → [24_host_incorreto.md], [25_source_inesperado.md]

### 🧠 Como pensar:
1. Eventos chegam no `_internal`?
2. O sourcetype foi configurado corretamente no `inputs.conf` ou `props.conf`?
3. Campos como `host`, `source`, `sourcetype`, `_time` aparecem corretamente?
4. Verificar `linebreaker`, `TIME_PREFIX`, `CHARSET`, `EXTRACT-*`, `FIELDALIAS-*`
5. Usar `btool` para revisar props ativos por sourcetype

---

## 🧠 Está tudo certo na ingestão, mas **os resultados das buscas não fazem sentido**?

➡️ **Busca está lenta?** → [03_search_lenta.md]  
  ↳ **Uso incorreto de subsearch?** → [14_subsearch_lento.md]  
➡️ **Busca retorna eventos duplicados?** → [08_duplicados_default_local.md]  
➡️ **Busca não traz resultado porque o path está errado?** → [09_caminho_conf_incorreto.md]  
➡️ **Busca sem retorno por indexação incompleta (bucket)?** → [05_buckets_congelado_prematuro.md]

### 🧠 Como pensar:
1. SPL está correto?
2. A janela de tempo cobre os eventos?
3. Index e sourcetype estão corretos?
4. Existe `join` ou `subsearch` muito pesado?
5. Verificar se buckets estão congelados ou deletados (`_audit`, `_internal`, `fs`)

---

## 🔔 O alerta ou agendamento **não dispara** ou não executa como esperado?

➡️ **Alertas com throttling ativo?** → [18_alerta_problems.md]  
➡️ **Scheduler pulando execuções?** → [18_alerta_problems.md]  
➡️ **Permissões insuficientes para executar alertas?** → [19_permissoes_erradas.md]

### 🧠 Como pensar:
1. Alertas aparecem em *Activity > Triggered Alerts*?
2. Ver `scheduler.log` para status da execução
3. Verificar `alert.suppress.*` no `savedsearches.conf`
4. A role do usuário tem `schedule_search`, `list_settings`, etc?
5. Macro ou base search quebrada?

---

## 🔁 **Forwarder não está enviando dados** corretamente?

➡️ **Conexão com o indexer está inativa?** → [21_fwd_broken.md]  
➡️ **Arquivo monitorado mas não aparece no index?** → [04_input_sem_ingestao.md], [20_permissao_arquivo_input.md]

### 🧠 Como pensar:
1. `splunk list forward-server` mostra status ativo?
2. Testar conectividade com `telnet`, `nc`, firewall
3. Logs no forwarder (`splunkd.log`) mostram erro?
4. Validar `outputs.conf`, `deploymentclient.conf`
5. Forwarder é pesado? Precisa de `indexAndForward`?

---

## 📦 **Arquitetura do Splunk quebrada ou inconsistente?**

➡️ **Problemas de replicação no SH Cluster?** → [16_sh_cluster.md]  
➡️ **KV Store não inicializa?** → [17_kvstore.md]  
➡️ **Configuração inválida (stanza malformada)?** → [26_invalid_stanza.md]  

### 🧠 Como pensar:
1. Verificar status de cluster via `splunk show shcluster-status`
2. Logs da KV Store (`mongod.log`)
3. Validar estrutura dos `.conf` via `btool`
4. Validar presença de stanzas mal formatadas

---

## 📉 **Dashboards não exibem dados**?

➡️ **Dashboards sem eventos mesmo com ingestão válida?** → [22_dash_vazio.md]  
➡️ **Base search com problema ou campo inexistente?** → [22_dash_vazio.md]

### 🧠 Como pensar:
1. Dashboard usa base search, `savedsearch`, `macros`?
2. Abrir painel no modo de edição → testar a SPL direto
3. O sourcetype e index usados existem?
4. Campos esperados (`src`, `action`, `status`) existem?

---

## 🛠️ **Problemas adicionais e específicos**?

➡️ **Lookup não retorna resultado?** → [11_lookup.md]  
➡️ **Permissão do objeto (role, index, etc.) está incorreta?** → [19_permissoes_erradas.md]  
➡️ **Logs do `_internal` estão vazios?** → [30_internallogs.md]

### 🧠 Como pensar:
1. Lookup populado? `| inputlookup nome_do_lookup`
2. Permissão de leitura/escrita no `collections.conf` ou `.csv`?
3. Role tem acesso ao objeto? `local.meta`/UI → Permissions
4. `_internal` vazio? Pode ser problema de roteamento ou falta de espaço

