# Simulação Core 15 – Campo `_time` ausente ou fixo

**🔹 Título:** Campo `_time` ausente ou com valor estático em todos os eventos

**❗ Problema:**
Os eventos estão sendo ingeridos, mas todos aparecem com o mesmo horário no campo `_time`, ou o campo `_time` está ausente, afetando buscas com base temporal.

**🧪 Causa Simulada:**
O Splunk não consegue identificar corretamente o timestamp nos eventos (por configuração incorreta ou ausência de timestamp no dado cru). Como fallback, aplica o horário de indexação.

**🔍 Passos de Investigação:**
1. Verificar eventos no index:
   ```spl
   index=main sourcetype=meu_sourcetype | table _time, _indextime, _raw
   ```
   → Notar se todos os `_time` são iguais ou seguem `_indextime`

2. Verificar se há timestamp no `_raw`:
   - Exemplo de linha com timestamp ausente:
     ```
     Login failed from 192.168.1.1
     ```

3. Validar o `props.conf` do sourcetype:
   ```ini
   [meu_sourcetype]
   TIME_PREFIX = ^
   TIME_FORMAT = %Y-%m-%dT%H:%M:%S
   MAX_TIMESTAMP_LOOKAHEAD = 25
   ```
   → Se mal definido, impede extração correta de `_time`

4. Analisar se está aplicando `DATETIME_CONFIG = CURRENT` (define `_time = _indextime`)
   ```bash
   ./splunk btool props list meu_sourcetype --debug | grep DATETIME_CONFIG
   ```

**🔧 Correção:**
1. Ajustar o `TIME_PREFIX`, `TIME_FORMAT` e `MAX_TIMESTAMP_LOOKAHEAD` no `props.conf`
2. Se não houver timestamp no dado, considerar usar `DATETIME_CONFIG = NONE` + `EVAL-_time`:
   ```ini
   DATETIME_CONFIG = NONE
   EVAL-_time = strptime(substr(_raw, 15, 19), "%Y-%m-%d %H:%M:%S")
   ```
3. Testar e reindexar (em ambiente de teste/lab)

**✅ Resultado Esperado:**
O campo `_time` passa a refletir corretamente a hora do evento, permitindo buscas temporais funcionais.

**💡 Lição Aprendida:**
O `_time` é base para todas as buscas temporais. Se o Splunk não conseguir interpretá-lo, o uso do dado fica extremamente limitado.
