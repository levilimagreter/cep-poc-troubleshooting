# Simulação Core 10 – Licença excedida e ingestão bloqueada

**🔹 Título:** Splunk para de indexar após exceder a cota de licença

**❗ Problema:**
De repente, o Splunk para de indexar novos dados. Os dashboards param de atualizar e nenhum evento novo aparece.

**🧪 Causa Simulada:**
A cota de ingestão da licença foi excedida por vários dias consecutivos, e o Splunk entrou em modo de bloqueio de indexação (violation lockout).

**🔍 Passos de Investigação:**
1. Verificar o status da licença via UI:
   - *Settings → Licensing → Licensing Reports*  
   - Conferir mensagens de *Violation* e *Stacked Violations*

2. Verificar via search:
   ```spl
   index=_internal source=*license_usage.log type=Usage | stats sum(b) by idx
   ```
   → Verifica quanto cada index consumiu

3. Verificar mensagens de erro nos logs:
   ```spl
   index=_internal source=*splunkd.log "license" OR "violation"
   ```
   → Exemplo: `license violation - indexer will stop indexing`

4. Validar se o ambiente entrou em *lockout*:
   ```spl
   | rest /services/licenser/status
   | table label, is_active, is_violation
   ```

**🔧 Correção:**
1. Solicitar licença de extensão temporária (se disponível) via Suporte Splunk.
2. Aplicar nova licença válida via:
   ```bash
   ./splunk add license /caminho/licenca.lic
   ```
3. Em caso de lab ou ambiente não produtivo, pode-se também reiniciar os contadores de violação (ambientes *trial*):
   ```bash
   ./splunk restart
   ```

**✅ Resultado Esperado:**
Após aplicar nova licença, a ingestão de dados é retomada e os dados voltam a ser indexados normalmente.

**💡 Lição Aprendida:**
O monitoramento do uso de licença deve ser proativo. Exceder o limite por 5 dias consecutivos leva ao bloqueio total de indexação.

