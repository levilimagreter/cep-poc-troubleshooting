# Simulação Core 04 – Input configurado, mas sem ingestão

**🔹 Título:** Monitoramento configurado, mas dados não aparecem (problema de permissão no arquivo)

**❗ Problema:**
Foi configurado um `monitor` no `inputs.conf` apontando para um arquivo de log, mas nenhum dado é ingerido pelo Splunk.

**🧪 Causa Simulada:**
O arquivo possui permissões de sistema que impedem o usuário do Splunk (`splunk`) de acessá-lo. O input está correto, mas o sistema operacional bloqueia a leitura.

**🔍 Passos de Investigação:**
1. Validar `inputs.conf` com `btool`:
   ```bash
   ./splunk btool inputs list --debug | grep -A 5 "[monitor"
   ```
2. Verificar o caminho do arquivo monitorado:
   ```bash
   ls -l /caminho/para/logs/
   ```
   Exemplo de saída problemática:
   ```
   -rw------- 1 root root 3021 mar 22 12:00 app.log
   ```
3. Verificar logs internos:
   ```spl
   index=_internal sourcetype=splunkd component=TailReader
   ```
   Mensagem típica:
   `File not readable — permission denied`

**🔧 Correção:**
Alterar permissões do arquivo para que o usuário do Splunk possa ler:
```bash
chown splunk:splunk /caminho/para/logs/app.log
chmod 644 /caminho/para/logs/app.log
```
Ou, se necessário:
```bash
setfacl -m u:splunk:r /caminho/para/logs/app.log
```
Reiniciar o Splunk se o monitoramento não recomeçar automaticamente:
```bash
./splunk restart
```

**✅ Resultado Esperado:**
Após corrigir as permissões, o arquivo é lido corretamente e os eventos passam a ser ingeridos.

**💡 Lição Aprendida:**
Inputs bem configurados ainda podem falhar se o sistema de arquivos não permitir leitura. Logs internos (`TailReader`) são fundamentais para identificar problemas de permissão.
