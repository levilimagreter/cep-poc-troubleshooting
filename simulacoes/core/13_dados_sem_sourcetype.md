# Simulação Core 13 – Dados sendo ingeridos sem sourcetype definido

**🔹 Título:** Eventos chegam ao Splunk, mas sem `sourcetype` visível ou com valor genérico (`stash`, `unknown`)

**❗ Problema:**
Os eventos são visíveis no `index`, mas o campo `sourcetype` está ausente ou com valor inesperado (`stash`, `missing`, etc). Isso impede parsing correto e visualizações nos dashboards.

**🧪 Causa Simulada:**
O `sourcetype` não foi explicitamente definido na configuração de input (ex: via `inputs.conf`, `monitor`, `script`, ou ingest REST/API). Com isso, o Splunk aplica um valor padrão ou inválido.

**🔍 Passos de Investigação:**
1. Verificar eventos recentes com sourcetype anômalo:
   ```spl
   index=main | stats count by sourcetype
   ```
   → Identificar `sourcetype=stash`, `missing`, ou em branco.

2. Localizar os eventos crus:
   ```spl
   index=main sourcetype=stash OR NOT sourcetype=* | table _time, _raw, sourcetype
   ```

3. Validar o tipo de input usado:
   - `inputs.conf` ausente de `sourcetype=`
   - Ingestão via script sem definição de `sourcetype`
   - Uso de `collect` sem definir `sourcetype`:
     ```spl
     | collect index=main
     ```
     (→ resultará em `sourcetype=stash`)

**🔧 Correção:**
1. Definir explicitamente o `sourcetype` no `inputs.conf`:
   ```ini
   [monitor:///var/logs/meu_arquivo.log]
   sourcetype = meu_sourcetype
   index = main
   ```

2. Para scripts:
   - Definir `sourcetype` no `inputs.conf` ou no output JSON/curl/API

3. Para `collect`, usar a opção:
   ```spl
   | collect index=main sourcetype=meu_sourcetype
   ```

**✅ Resultado Esperado:**
Os novos eventos passam a ser ingeridos com o `sourcetype` correto, permitindo parsing e extrações associadas.

**💡 Lição Aprendida:**
O `sourcetype` é essencial para todo o pipeline de parsing. Sempre garanta que ele esteja corretamente definido no momento da ingestão.
