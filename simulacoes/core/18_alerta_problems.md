# Simulação Core 18 – Alerta não sendo disparado por throttling

**🔹 Título:** Alerta esperado não dispara por causa de `throttling`

**❗ Problema:**
Uma busca configurada como alerta deveria disparar notificações (e-mail, notável, script), mas mesmo com condições atendidas, nada acontece. O alerta aparece como executado no histórico, mas sem ação.

**🧪 Causa Simulada:**
O parâmetro de `throttle` foi configurado para impedir o disparo de alertas com campos repetidos em um intervalo de tempo. Por isso, alertas legítimos estão sendo silenciados.

**🔍 Passos de Investigação:**
1. Verificar o histórico de execução do alerta:
   - *Settings → Searches, Reports, and Alerts → Your Alert → View Recent*  
   - Status: *skipped* ou *success* sem action

2. Validar o `savedsearches.conf`:
   ```ini
   [Your Alert Name]
   action.email = 1
   alert.track = 1
   alert.suppress = 1
   alert.suppress.fields = host
   alert.suppress.period = 1h
   ```
   → Isso suprime alertas com mesmo `host` dentro de 1 hora

3. Verificar no `scheduler.log` o comportamento do alerta:
   ```spl
   index=_internal sourcetype=scheduler savedsearch_name="Your Alert Name"
   ```
   → Buscar por mensagens como `Search skipped due to throttling`

4. Analisar se os eventos gerados têm campo repetido (`host`, `user`, etc)

**🔧 Correção:**
1. Ajustar ou desabilitar o `throttling`, se não for necessário:
   - Pela UI: desmarcar *Throttle* nas configurações do alerta
   - Ou via `savedsearches.conf`:
     ```ini
     alert.suppress = 0
     ```

2. Testar o alerta manualmente com `Run Search` para validar execução completa

**✅ Resultado Esperado:**
Alertas voltam a ser acionados normalmente conforme os critérios da busca, sem bloqueio indevido.

**💡 Lição Aprendida:**
`Throttling` é útil para evitar alertas em excesso, mas pode mascarar problemas reais se aplicado sem atenção. Sempre revise os campos usados para suprimir alertas.

