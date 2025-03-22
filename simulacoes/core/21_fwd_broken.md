# Simulação Core 21 – Forward-server com conexão quebrada

**🔹 Título:** Universal Forwarder não está enviando dados – conexão quebrada com indexer

**❗ Problema:**
O Universal Forwarder está instalado e configurado, mas nenhum dado chega ao indexer. O comando `splunk list forward-server` mostra status como `inactive` ou não listado.

**🧪 Causa Simulada:**
A configuração de forwarding (`outputs.conf`) está incorreta ou o indexer não está escutando na porta esperada (9997). Pode ser também firewall ou erro de DNS.

**🔍 Passos de Investigação:**
1. Verificar status do forward-server:
   ```bash
   ./splunk list forward-server
   ```
   → Resultado esperado:
   ```
   Active forwards:
       indexer.lab:9997 (ssl:disabled)
   ```
   → Se vazio ou `inactive`, há problema na conexão.

2. Validar conectividade:
   ```bash
   telnet indexer.lab 9997
   nc -zv indexer.lab 9997
   ```

3. Verificar `outputs.conf`:
   ```bash
   cat $SPLUNK_HOME/etc/system/local/outputs.conf
   ```
   Exemplo incorreto:
   ```ini
   [tcpout]
   defaultGroup = default-autolb-group

   [tcpout:default-autolb-group]
   server = indexer.labb:9997   # erro de digitação no hostname
   ```

4. Conferir logs do forwarder:
   ```bash
   tail -n 50 $SPLUNK_HOME/var/log/splunk/splunkd.log
   ```
   → Mensagens como `TcpOutputProc - Connection to indexer.labb failed`

**🔧 Correção:**
1. Corrigir o `outputs.conf` com o hostname correto:
   ```ini
   [tcpout:default-autolb-group]
   server = indexer.lab:9997
   ```

2. Reiniciar o forwarder:
   ```bash
   ./splunk restart
   ```

3. Certificar-se de que o indexer está escutando na porta 9997:
   ```bash
   netstat -plnt | grep 9997
   ```

**✅ Resultado Esperado:**
Após correção, o comando `splunk list forward-server` exibe status ativo e os dados passam a chegar ao indexer.

**💡 Lição Aprendida:**
Conectividade entre forwarder e indexer é essencial. Pequenos erros no `outputs.conf` ou problemas de rede/firewall podem impedir totalmente a ingestão de dados.
