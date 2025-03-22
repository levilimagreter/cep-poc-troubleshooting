# Simulação Core 20 – Input configurado, mas arquivo inacessível (permissão Linux)

**🔹 Título:** Splunk não consegue monitorar arquivo por falta de permissão de leitura no sistema operacional

**❗ Problema:**
Mesmo com o input configurado corretamente em `inputs.conf`, o arquivo de log não é ingerido. Nenhum evento é visível no index.

**🧪 Causa Simulada:**
O usuário que executa o Splunk (`splunk`) não tem permissão de leitura no arquivo monitorado. Isso impede a leitura via `monitor` input.

**🔍 Passos de Investigação:**
1. Verificar se o arquivo existe no caminho monitorado:
   ```bash
   ls -l /var/logs/app.log
   ```
   Exemplo de saída:
   ```
   -rw------- 1 root root 1200 mar 21 15:00 /var/logs/app.log
   ```

2. Validar se o usuário do Splunk pode ler:
   ```bash
   sudo -u splunk cat /var/logs/app.log
   ```
   → Se der `Permission denied`, esse é o problema.

3. Verificar logs internos:
   ```spl
   index=_internal sourcetype=splunkd component=TailReader
   ```
   → Procurar por mensagens como: `File not readable - permission denied`

**🔧 Correção:**
1. Ajustar permissões do arquivo:
   ```bash
   chown splunk:splunk /var/logs/app.log
   chmod 644 /var/logs/app.log
   ```

2. Alternativa: criar ACL específica para o usuário Splunk:
   ```bash
   setfacl -m u:splunk:r /var/logs/app.log
   ```

3. Garantir que diretórios-pai também sejam acessíveis:
   ```bash
   chmod +x /var/logs
   ```

4. Reiniciar Splunk (ou reindexar) se necessário:
   ```bash
   ./splunk restart
   ```

**✅ Resultado Esperado:**
O Splunk passa a monitorar corretamente o arquivo e os eventos começam a ser ingeridos no index.

**💡 Lição Aprendida:**
Mesmo com configuração correta, o sistema operacional pode impedir o Splunk de acessar arquivos. Logs internos (`TailReader`) e permissões Linux devem sempre ser verificados em casos de falha de ingestão.

