# Simulação Core 14 – Busca lenta por uso de `append`/`subsearch`

**🔹 Título:** Subsearch com `append` deixa a busca muito lenta

**❗ Problema:**
Uma busca que combina resultados de múltiplos índices usando `append` está demorando minutos para retornar, mesmo com filtros de tempo curtos.

**🧪 Causa Simulada:**
A subsearch está retornando um volume muito grande de eventos, sendo executada em background e causando lentidão. O `append` executa a subsearch primeiro, e depois a principal, dificultando o paralelismo e otimizador da SPL.

**🔍 Passos de Investigação:**
1. Revisar a busca que usa `append`:
   ```spl
   search index=firewall action=blocked
   | append [ search index=auth action=failed ]
   ```

2. Analisar com o Job Inspector:
   - Verificar o tempo total de subsearch
   - Conferir mensagens como "subsearch expanded to >50k rows"

3. Avaliar o tamanho da subsearch isoladamente:
   ```spl
   search index=auth action=failed | stats count
   ```

4. Testar se subsearch pode ser reescrita com `OR`, `stats`, ou `inputlookup`

**🔧 Correção:**
Reescrever usando `OR` ou `stats`, permitindo melhor paralelização:
```spl
index=firewall action=blocked OR index=auth action=failed
| stats count by index, action
```
Ou, se dados forem predefinidos:
```spl
| inputlookup acessos_bloqueados.csv
| append [ inputlookup falhas_login.csv ]
```
(para casos controlados com lookup)

**✅ Resultado Esperado:**
Busca passa a ser executada com muito mais rapidez, aproveitando o paralelismo e otimizador de queries do Splunk.

**💡 Lição Aprendida:**
`append` e `subsearch` devem ser evitados com conjuntos grandes de dados. Use `OR`, `stats`, `lookup`, ou `union` quando possível.
