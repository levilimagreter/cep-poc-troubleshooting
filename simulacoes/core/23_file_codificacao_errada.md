# Simulação Core 23 – Arquivo monitorado com codificação inválida

**🔹 Título:** Splunk não consegue ingerir corretamente arquivos com codificação inválida

**❗ Problema:**
Eventos de um arquivo de log monitorado são ingeridos com caracteres corrompidos, truncados ou nem aparecem no index. 

**🧪 Causa Simulada:**
O arquivo possui uma codificação de caracteres incompatível com o padrão esperado pelo Splunk (ex: UTF-16, UTF-32, ou codificações regionais como ISO-8859-1).

**🔍 Passos de Investigação:**
1. Validar visualmente se os eventos ingeridos estão truncados ou corrompidos:
   ```spl
   index=main sourcetype=meu_sourcetype | table _raw
   ```

2. Checar codificação do arquivo no sistema operacional:
   ```bash
   file -i /caminho/para/logs/app.log
   ```
   Exemplo de saída:
   ```
   app.log: text/plain; charset=utf-16le
   ```

3. Tentar abrir o arquivo com `cat` e `less`:
   ```bash
   cat app.log
   less app.log
   ```
   → Pode aparecer ilegível ou com caracteres estranhos

4. Verificar se o Splunk está ignorando o arquivo no `splunkd.log`:
   ```spl
   index=_internal sourcetype=splunkd "invalid encoding"
   ```

**🔧 Correção:**
1. Converter o arquivo para UTF-8:
   ```bash
   iconv -f UTF-16 -t UTF-8 app.log -o app_utf8.log
   ```
   Ou:
   ```bash
   recode UTF-16..UTF-8 app.log
   ```

2. Alterar o input para apontar para o novo arquivo convertido

3. Reindexar os dados se necessário (em ambiente de teste)

**✅ Resultado Esperado:**
Após conversão para UTF-8, os eventos são ingeridos corretamente, com os caracteres e campos preservados.

**💡 Lição Aprendida:**
O Splunk espera arquivos em UTF-8 por padrão. Validar e converter a codificação antes da ingestão é uma boa prática, especialmente com logs gerados por sistemas legados ou internacionais.

