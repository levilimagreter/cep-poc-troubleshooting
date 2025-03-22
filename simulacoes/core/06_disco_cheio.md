# Simulação Core 06 – splunkd parando por falta de espaço em disco

**🔹 Título:** splunkd para de funcionar por disco cheio

**❗ Problema:**
O Splunk parou de indexar e responder a requisições. Usuários não conseguem acessar a interface. Ao tentar iniciar o serviço manualmente, ele falha.

**🧪 Causa Simulada:**
O disco onde estão armazenados os índices (`$SPLUNK_DB`) está completamente cheio, impedindo o funcionamento normal do `splunkd`.

**🔍 Passos de Investigação:**
1. Verificar o uso de disco no sistema:
   ```bash
   df -h
   ```
   Saída típica:
   ```
   /dev/sda2        100%    /opt/splunk/var
   ```

2. Verificar se o processo `splunkd` está rodando:
   ```bash
   ps -ef | grep splunkd
   ```

3. Tentar iniciar manualmente o Splunk:
   ```bash
   ./splunk start
   ```
   Mensagem típica:
   `Splunkd daemon is not responding: check splunkd.log for details`

4. Ver logs recentes:
   ```bash
   tail -n 50 $SPLUNK_HOME/var/log/splunk/splunkd.log
   ```
   Possível erro:
   `Unable to write to index directory, no space left on device`

**🔧 Correção:**
1. Liberar espaço em disco:
   - Remover arquivos antigos de log:
     ```bash
     find /opt/splunk/var/log -type f -name "*.log" -mtime +10 -delete
     ```
   - Mover buckets antigos para backup externo (se aplicável).

2. Se necessário, aumentar espaço do volume ou mover `$SPLUNK_DB` para outra partição com mais espaço.

3. Iniciar novamente o Splunk:
   ```bash
   ./splunk start
   ```

**✅ Resultado Esperado:**
O Splunk volta a funcionar normalmente após liberação de espaço em disco.

**💡 Lição Aprendida:**
Splunk precisa de espaço em disco para funcionar. Monitorar o uso de disco e configurar alertas proativos pode prevenir indisponibilidade total do serviço.
