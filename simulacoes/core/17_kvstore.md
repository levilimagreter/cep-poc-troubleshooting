# Simulação Core 17 – KV Store não inicializa corretamente

**🔹 Título:** KV Store falha ao iniciar, impedindo funcionamento de objetos dependentes

**❗ Problema:**
Apps que dependem da KV Store (como ITSI, ES ou custom lookups) falham ao carregar. Erros relacionados a `lookup table is empty`, falhas em `inputlookup`, ou ausência de objetos configurados.

**🧪 Causa Simulada:**
A instância do Splunk não consegue iniciar o serviço de KV Store, geralmente por:
- Corrupção de arquivos MongoDB
- Porta em uso (8191 por padrão)
- Permissões de diretório incorretas
- Falta de espaço em disco

**🔍 Passos de Investigação:**
1. Verificar status da KV Store:
   ```bash
   ./splunk show kvstore-status
   ```
   → Esperado: `running`, mas pode aparecer `stopped`, `failed`, `waiting`

2. Checar logs da KV Store:
   ```bash
   tail -n 50 $SPLUNK_HOME/var/log/splunk/kvstore.log
   ```

3. Validar se a porta 8191 está em uso:
   ```bash
   netstat -tulnp | grep 8191
   ```

4. Conferir espaço em disco e permissões:
   ```bash
   df -h
   ls -l $SPLUNK_HOME/var/lib/splunk/kvstore/mongo
   ```

5. Verificar erros no `splunkd.log`:
   ```spl
   index=_internal source=*splunkd.log "kvstore"
   ```

**🔧 Correção:**
1. Reiniciar apenas o KV Store:
   ```bash
   ./splunk restart kvstore
   ```

2. Se houver corrupção:
   - Parar o Splunk:
     ```bash
     ./splunk stop
     ```
   - Mover diretório da KV Store (backup):
     ```bash
     mv $SPLUNK_HOME/var/lib/splunk/kvstore $SPLUNK_HOME/var/lib/splunk/kvstore_bkp
     ```
   - Iniciar novamente:
     ```bash
     ./splunk start
     ```
     (o Splunk recria o MongoDB limpo)

**✅ Resultado Esperado:**
A KV Store inicializa corretamente e os objetos baseados nela passam a funcionar normalmente.

**💡 Lição Aprendida:**
A KV Store é crítica para muitos apps. Monitorar seu estado e manter backups dos dados salvos são essenciais para ambientes produtivos.
