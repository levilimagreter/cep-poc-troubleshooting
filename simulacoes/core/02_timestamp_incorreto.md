# Simulação Core 02 – Timestamp incorreto nos eventos

**🔹 Título:** Eventos com timestamp no futuro não aparecem nas buscas padrão

**❗ Problema:**
Após configurar um input de log, os eventos estão sendo ingeridos, mas não aparecem nas buscas padrão (ex: `últimos 15 minutos`).

**🧪 Causa Simulada:**
O campo de timestamp (`_time`) dos eventos está sendo interpretado incorretamente devido a má configuração do `TIME_PREFIX` e `TIME_FORMAT`, resultando em datas futuras.

**🔍 Passos de Investigação:**
1. Buscar pelos eventos em uma janela ampla de tempo:
   ```spl
   index=main earliest=-7d@d latest=+7d@d
   ```
2. Validar o campo `_time`:
   ```spl
   index=main | eval now=now() | table _time, now, _raw
   ```
3. Verificar qual sourcetype está sendo aplicado:
   ```spl
   index=main | stats count by sourcetype
   ```
4. Revisar configuração do `props.conf` para o sourcetype:
   ```
   [meu_sourcetype]
   TIME_PREFIX = ^
   TIME_FORMAT = %Y-%m-%d %H:%M:%S
   MAX_TIMESTAMP_LOOKAHEAD = 20
   ```
5. Conferir se o timestamp no raw log está compatível com o `TIME_FORMAT` usado.

**🔧 Correção:**
Ajustar o `props.conf` para refletir corretamente o formato do timestamp real dos eventos. Exemplo:
```ini
[meu_sourcetype]
TIME_PREFIX = "timestamp":\s*
TIME_FORMAT = %Y-%m-%dT%H:%M:%S
MAX_TIMESTAMP_LOOKAHEAD = 25
```
Após ajustar:
```bash
./splunk restart
```
E reindexar os eventos (se necessário para fins de teste).

**✅ Resultado Esperado:**
Os eventos passam a ter o campo `_time` corretamente interpretado e aparecem nas janelas de busca padrão.

**💡 Lição Aprendida:**
Mesmo que os dados estejam sendo ingeridos, uma má configuração de timestamp pode ocultar eventos das buscas. Validar `_time` logo após a ingestão é essencial para troubleshooting.

