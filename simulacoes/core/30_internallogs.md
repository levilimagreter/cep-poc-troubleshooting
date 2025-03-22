# Simulação Core 30 – `index=_internal` não mostra atividade esperada (monitoramento quebrado)

**🔹 Título:** `_internal` parece vazio – logs de monitoramento não aparecem

**❗ Problema:**
O index `_internal`, normalmente usado para diagnóstico e monitoramento, não está retornando resultados esperados. Dashboards de health monitoring e auditoria estão vazios.

**🧪 Causa Simulada:**
O componente que deveria gerar os logs (`splunkd`, `scheduler`, `metrics`, etc.) não está escrevendo corretamente no `_internal`, ou há filtro de ingestão que redireciona estes eventos para outro index. Também pode haver falha de rota ou sobreposição no `outputs.conf` em ambientes com forwarders.

**🔍 Passos de Investigação:**
1. Tentar buscas básicas no `_internal`:
   ```spl
   index=_internal | stats count by sourcetype
   ```
   → Esperado: `splunkd`, `metrics`, `scheduler`, etc.

2. Validar se o `_internal` está habilitado:
   - Em `outputs.conf`, certificar-se que a stança `[tcpout]` **não redireciona `_internal`** para outro lugar

3. Em ambientes com Heavy Forwarders ou UFs, validar roteamento de indexes:
   ```ini
   [indexAndForward]
   index = false
   ```
   → Pode estar impedindo indexação local

4. Verificar se há erro nos logs de forwarding:
   ```bash
   tail -n 100 $SPLUNK_HOME/var/log/splunk/splunkd.log
   ```

5. Verificar espaço em disco no `SPLUNK_DB`:
   ```bash
   df -h
   ```
   → `_internal` pode ter falhado silenciosamente por falta de espaço

**🔧 Correção:**
1. Garantir que os dados do `_internal` sejam mantidos localmente ou roteados corretamente
2. Verificar configurações em `outputs.conf`, `props.conf`, `transforms.conf` que possam afetar
3. Reiniciar Splunk após correções:
   ```bash
   ./splunk restart
   ```

**✅ Resultado Esperado:**
O index `_internal` volta a exibir logs de sistema e monitoramento normalmente.

**💡 Lição Aprendida:**
O `_internal` é crítico para troubleshooting. Qualquer configuração que afete sua indexação compromete a observabilidade do ambiente. Validar seu funcionamento deve ser parte do checklist pós-instalação e durante a operação.

