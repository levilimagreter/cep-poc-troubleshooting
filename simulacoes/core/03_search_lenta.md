# Simulação Core 03 – Search lenta por uso de join

**🔹 Título:** Busca com desempenho ruim devido a uso de `join`

**❗ Problema:**
Um dashboard demora vários minutos para carregar. A busca principal utiliza `join` entre dois conjuntos de dados grandes.

**🧪 Causa Simulada:**
A cláusula `join` está sendo usada entre dois índices com alto volume e sem otimização (sem restrição de tempo ou `fields`). Isso força o Splunk a fazer operações de memória custosas.

**🔍 Passos de Investigação:**
1. Usar o *Search Job Inspector*:
   - Menu da busca → *Inspect Job*
   - Verificar etapas com maior duração (ex: `command.join`)

2. Analisar a query:
   ```spl
   index=web_logs status=500
   | join session_id [ search index=auth_logs action=login ]
   ```

3. Avaliar se `join` pode ser substituído por `stats`, `lookup` ou `append`.

4. Verificar se o subsearch retorna volume excessivo:
   ```spl
   [ search index=auth_logs action=login | stats count ]
   ```
   > Lembre-se: limite de 50.000 linhas por subsearch por padrão.

**🔧 Correção:**
Reescrever a busca para evitar `join`, usando `stats` com `by session_id`, ou `lookup` intermediário.

Exemplo de reescrita com `stats`:
```spl
index=web_logs status=500
| stats count by session_id
| join type=inner session_id [ search index=auth_logs action=login | stats count by session_id ]
```
Ou:
```spl
index=web_logs status=500 OR (index=auth_logs action=login)
| stats values(action) as actions by session_id
| where "login" IN actions AND "500" IN actions
```

**✅ Resultado Esperado:**
A busca se torna muito mais rápida, e os dashboards carregam em segundos.

**💡 Lição Aprendida:**
`join` é poderoso, mas perigoso em buscas com grande volume. Sempre que possível, prefira `stats`, `lookup` ou `append` para otimizar a performance.
