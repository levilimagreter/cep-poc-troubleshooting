# Fluxo de Troubleshooting – Infosec (Árvore Decisória + Raciocínio Técnico)

Este fluxo te ajuda a navegar por problemas comuns enfrentados ao usar o **Splunk App for Information Security (Infosec)**. Ele foi desenhado com base nos 30 cenários simulados para oferecer um caminho estruturado de análise.

---

## 📊 Dashboards do Infosec estão **vazios**?

➡️ **Instalação recente e nenhum painel mostra dados?** → [01_dashboards_vazios.md]  
➡️ **Campos `tag=authentication`, `tag=attack`, etc., não existem?** → [02_tags_ausentes.md]  
➡️ **Lookup com campos em branco?** → [03_lookup_nao_preenchido.md]  
➡️ **Alertas configurados mas não disparam?** → [04_alertas_nao_disparam.md]  
➡️ **Usuário sem acesso a macros, savedsearches ou painéis?** → [05_permissoes_macro_objetos.md]

### 🧠 Como pensar:
1. O app está instalado e atualizado corretamente?
2. Há dados nos indexes esperados?
3. Os campos exigidos pelas macros estão nos eventos (`tag`, `src`, `user`, etc)?
4. Os lookups foram preenchidos? Estão agendados corretamente?
5. O usuário tem permissão para visualizar os objetos do app?

---

## 🔎 As **buscas** ou **painéis** estão em branco (parcialmente)?

➡️ **Index não está configurado ou está errado?** → [06_index_nao_configurado.md]  
➡️ **Search retorna 0 eventos, mesmo com dados?** → [07_search_nao_retornando.md]  
➡️ **Macro usada em painel está com erro de sintaxe?** → [08_macro_erro_sintaxe.md]  
➡️ **Filtro de tempo muito restritivo?** → [09_filtro_temporal_exclui_dados.md]  
➡️ **Sourcetype usado nos dados não é mapeado pelo app?** → [10_sourcetype_nao_mapeado.md]

### 🧠 Como pensar:
1. Testar `| tstats` ou `index=*` no período desejado
2. Expandir macros (`| makeresults | eval search="`macro_name`"`)
3. Conferir sourcetypes e tags aplicados
4. Validar permissões dos painéis e macros
5. A base search do painel realmente retorna algo?

---

## 📉 Dados existem, mas **a correlação/comportamento esperado não ocorre**?

➡️ **Eventos não estão agrupados por tipo (`eventtype` inválido)?** → [11_eventtype_invalido.md]  
➡️ **Dashboards com painéis que usam `summary index` sem dados?** → [12_summary_index_vazio.md]  
➡️ **Campos esperados (`user`, `src`, `action`) ausentes?** → [13_falta_de_dados_esperados.md], [15_campo_esperado_ausente.md]  
➡️ **Erro específico em painel customizado?** → [14_erro_em_painel_customizado.md]

### 🧠 Como pensar:
1. As regras de correlação exigem campos ou normalização?
2. Há modelo de dados ou tags esperadas ausentes?
3. Há `eventtypes` definidos que não se aplicam aos dados reais?
4. O summary index está sendo populado? Há savedsearch ativa?

---

## 🚨 Alertas, tokens ou correlações **não funcionam corretamente**?

➡️ **Regras de correlação estão desativadas?** → [16_regras_correlacao_inativas.md]  
➡️ **Agendamento da busca está errado?** → [17_tempo_agendamento_ruim.md]  
➡️ **Tokens não resolvem no painel ou alerta?** → [18_token_nao_resolvido.md]  
➡️ **Base search usada em painel está inválida?** → [19_base_search_invalida.md]  
➡️ **Permissões do lookup impedem leitura/escrita?** → [20_lookup_perm_errada.md]

### 🧠 Como pensar:
1. Buscar no `_audit` se a savedsearch executou corretamente
2. Validar tokens no XML do painel (`$token$`) e macros associadas
3. Revalidar os objetos compartilhados no escopo correto (app, user, global)
4. Ver logs no `_internal` sobre falhas de execução (search scheduler)

---

## 🧩 Objetos do app **não funcionam como esperado**?

➡️ **Macro chamada no painel não existe?** → [21_macro_faltando.md]  
➡️ **Dashboard customizado está com erro de referência?** → [22_dashboard_personalizado_quebrado.md]  
➡️ **Lookup usado em tempo de execução não é automático?** → [23_lookup_nao_automatico.md]  
➡️ **Lookup está desatualizado?** → [24_lookup_desatualizado.md]  
➡️ **Painel traz estatísticas incorretas (base errada)?** → [25_estatistica_incorreta_painel.md]

### 🧠 Como pensar:
1. Validar `inputlookup` manualmente
2. Lookup está sendo atualizado por savedsearch?
3. A macro está publicada no escopo certo?
4. `props.conf` e `transforms.conf` foram validados?

---

## 🛑 Há **comportamentos inesperados no app**?

➡️ **Eventos duplicados causam alertas em excesso?** → [26_eventos_duplicados_alerta.md]  
➡️ **Painel só mostra dados para certos usuários?** → [27_filtro_usuario_restritivo.md]  
➡️ **Usuário não enxerga painéis ou resultados?** → [28_objetos_nao_compartilhados.md]  
➡️ **Tags conflitantes nos eventos?** → [29_tags_conflitantes.md]  
➡️ **Alertas disparam com alta frequência indevida?** → [30_alertas_muito_sensiveis.md]

### 🧠 Como pensar:
1. Eventos têm identificador único? Há duplicação?
2. Roles dos usuários têm acesso ao app e index correto?
3. Os objetos do app estão com permissão correta? (ver `local.meta`)
4. As condições de alerta consideram volume, agregação ou suppress?

---

> 💡 **Dica final:** Sempre que estiver em dúvida, comece investigando os campos fundamentais dos eventos (`index`, `sourcetype`, `tag`, `user`, `src`, `action`) e o comportamento esperado das macros e lookups que o app usa.

