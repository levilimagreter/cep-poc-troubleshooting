# Simulação Core 22 – Dados no index, mas dashboards vazios (erro de base search)

**🔹 Título:** Eventos estão presentes no index, mas dashboards não exibem resultados

**❗ Problema:**
Mesmo com dados confirmados no `index=main`, os dashboards permanecem vazios ou exibem “No results found”.

**🧪 Causa Simulada:**
Os dashboards utilizam uma `base search` mal configurada ou com filtros rígidos demais. Campos esperados não estão presentes ou o `sourcetype` está incorreto.

**🔍 Passos de Investigação:**
1. Confirmar que há eventos no index:
   ```spl
   index=main earliest=-15m
   ```

2. Inspecionar os painéis que não mostram dados:
   - Acessar via UI e clicar em *Open in Search*
   - Verificar se a `base search` está correta:
     ```spl
     base | search sourcetype=cisco:asa action=blocked
     ```
     → `base` pode estar retornando zero resultados

3. Verificar uso de `| loadjob` ou `| savedsearch` mal referenciado

4. Validar o `macros.conf` (se o dashboard usa macro)

5. Conferir campos exigidos nos painéis:
   - O painel usa `src`, `dest`, `action`, etc., que não estão presentes nos dados reais

**🔧 Correção:**
1. Ajustar a base search do dashboard:
   - Usar uma busca genérica para teste:
     ```spl
     index=main | stats count by sourcetype
     ```

2. Garantir que os campos referenciados estejam sendo extraídos corretamente

3. Se `macros` forem usados, testar manualmente sua expansão:
   ```spl
   `nome_da_macro`
   ```

4. Revisar o XML do dashboard e confirmar dependências entre painéis/base searches

**✅ Resultado Esperado:**
Os painéis voltam a exibir dados corretamente, refletindo os eventos já presentes no index.

**💡 Lição Aprendida:**
Dashboards podem parecer quebrados mesmo com dados presentes. Validar base search, campos esperados e macros é essencial para garantir a visibilidade correta.
