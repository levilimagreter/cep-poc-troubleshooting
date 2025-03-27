# Simulação Core 01 – Dados não aparecem no index

**🔹 Título:** Dados não aparecem no index após configuração de input

**❗ Problema:**
Após configurar um monitoramento de log via `inputs.conf`, os dados não aparecem no index esperado (`index=main`).

**🧪 Causa Simulada:**
O `inputs.conf` está apontando para um índice que não existe (`index=fakeindex`).

**🔍 Passos de Investigação:**
1. Verificar o `inputs.conf` com `btool`:
   ```bash
   ./splunk btool inputs list --debug | grep -A 5 "[monitor"  
   ```
2. Validar se o index `fakeindex` existe:
   ```spl
   | eventcount summarize=false index=fakeindex
   ```
3. Conferir logs internos:
   ```spl
   index=_internal sourcetype=splunkd component=MetricsProcessor
   ```
4. Verificar se o forwarder está enviando dados corretamente:
   ```bash
   ./splunk list forward-server
   ./splunk display listen
   ```

**🔧 Correção:**
Editar o `inputs.conf` para usar um índice válido (ex: `index=main`) e reiniciar o Splunk:
```bash
vim $SPLUNK_HOME/etc/system/local/inputs.conf
# Corrigir: index=main
./splunk restart
```

**✅ Resultado Esperado:**
Após a correção, os dados começam a aparecer normalmente em `index=main`.

**💡 Lição Aprendida:**
Erros simples como o nome de um índice incorreto podem causar ausência total de dados visíveis. Usar `btool` e `_internal` acelera muito o diagnóstico.

Splunk Doc:

https://docs.splunk.com/Documentation/Splunk/latest/SearchReference/Eventcount
https://docs.splunk.com/Documentation/Splunk/latest/Troubleshooting/Usebtooltotroubleshootconfigurations
