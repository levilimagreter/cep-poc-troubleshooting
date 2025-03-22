# Simulação Core 28 – Arquivo `.gz` não sendo descomprimido e lido

**🔹 Título:** Arquivo compactado `.gz` não é ingerido pelo Splunk como esperado

**❗ Problema:**
Um arquivo `.gz` foi movido para o diretório monitorado pelo Splunk, mas os dados não foram indexados. Mesmo após aguardar alguns minutos, os eventos não aparecem.

**🧪 Causa Simulada:**
O arquivo `.gz` foi adicionado após o Splunk já ter processado a versão descompactada (mesmo nome base). O Splunk mantém um controle para não reprocessar arquivos já ingeridos, mesmo que venham em formato diferente. Também pode ocorrer por uso incorreto do monitoramento.

**🔍 Passos de Investigação:**
1. Validar se o arquivo `.gz` está no diretório monitorado:
   ```bash
   ls -lh /opt/logs/app/*.gz
   ```

2. Conferir se a data de modificação do arquivo é anterior ao início do monitoramento

3. Verificar se há arquivos `.crc` no diretório:
   ```bash
   ls -a /opt/logs/app | grep crc
   ```
   → O Splunk usa arquivos `.crc` para evitar ingestão duplicada

4. Validar se o monitoramento permite arquivos compactados:
   - O Splunk por padrão suporta `.gz` via `monitor` (não via `batch`)

5. Verificar logs internos:
   ```spl
   index=_internal sourcetype=splunkd component=FileClassifierManager
   ```
   → Procurar por mensagens indicando que o arquivo foi ignorado

**🔧 Correção:**
1. Renomear o `.gz` com um nome exclusivo:
   ```bash
   mv app.log.gz app_20250322.log.gz
   ```
   → Garante que o Splunk o veja como novo

2. (Opcional) Remover o arquivo `.crc` correspondente:
   ```bash
   rm /opt/logs/app/.app.log.gz.crc
   ```

3. Certificar-se de que o input usa `monitor`, não `batch`

**✅ Resultado Esperado:**
Após renomear (ou remover `.crc`), o Splunk descompacta e ingere o arquivo `.gz` normalmente, e os eventos aparecem no index.

**💡 Lição Aprendida:**
O Splunk evita reprocessar arquivos com base em checksums. Para ingerir `.gz` corretamente, eles devem ser únicos e monitorados por input do tipo `monitor`.

