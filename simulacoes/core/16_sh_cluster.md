# Simulação Core 16 – Search Head Cluster com replicação quebrada

**🔹 Título:** Problema de replicação no Search Head Cluster (SHC)

**❗ Problema:**
Em um ambiente com Search Head Cluster (SHC), os saved searches, dashboards e knowledge objects criados em um nó não aparecem nos demais membros do cluster.

**🧪 Causa Simulada:**
A replicação entre os membros do SHC está quebrada, muitas vezes causada por erro de configuração, falha de comunicação, ou conflitos no `server.conf` / `shcluster-config`.

**🔍 Passos de Investigação:**
1. Verificar o status do cluster:
   ```bash
   ./splunk show shcluster-status
   ```
   → Verificar se há membros “Pending” ou “Out of Sync”

2. Inspecionar logs de replicação:
   ```spl
   index=_internal sourcetype=shclustering
   ```
   → Mensagens como `Failed to push configuration` ou `replication error`

3. Verificar a configuração dos membros:
   ```bash
   ./splunk btool server list shclustering --debug
   ```
   → Validar `conf_deploy_fetch_url`, `mgmt_uri`, `shcluster_label`, etc.

4. Conferir se o `Captain` está ativo:
   ```bash
   ./splunk show shcluster-status | grep captain
   ```

5. Validar horário/sincronização entre os nós (`ntp`, `date`) – clocks desincronizados causam problemas.

**🔧 Correção:**
1. Corrigir configurações de replicação nos nós afetados.
2. Sincronizar relógios entre os membros (`ntpdate`, `chrony`).
3. Forçar replicação manual:
   ```bash
   ./splunk apply shcluster-bundle -target https://captain:8089 -auth admin:senha
   ```
4. Reiniciar membros se necessário:
   ```bash
   ./splunk restart
   ```

**✅ Resultado Esperado:**
Os knowledge objects passam a ser replicados corretamente entre os membros do SHC.

**💡 Lição Aprendida:**
Clusters de Search Head exigem configuração precisa e consistência entre os nós. Monitorar o `shclustering.log` e manter os relógios sincronizados são boas práticas para evitar falhas de replicação.

