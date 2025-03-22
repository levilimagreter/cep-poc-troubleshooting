# Simulação Core 05 – Buckets congelando prematuramente

**🔹 Título:** Eventos antigos desaparecendo por congelamento antecipado de buckets

**❗ Problema:**
Eventos antigos desaparecem mesmo com configurações padrão. Ao buscar por dados com mais de X dias, nada é retornado.

**🧪 Causa Simulada:**
Parâmetros de retenção no `indexes.conf` foram configurados incorretamente, fazendo os buckets serem congelados após poucos dias (`frozenTimePeriodInSecs` muito baixo).

**🔍 Passos de Investigação:**
1. Validar a existência dos dados:
   ```spl
   index=main earliest=-30d@d
   ```
2. Verificar se o `frozenTimePeriodInSecs` está configurado:
   ```bash
   ./splunk btool indexes list main --debug | grep frozenTimePeriodInSecs
   ```
3. Checar o diretório de buckets:
   ```bash
   ls $SPLUNK_DB/maindb/db
   ls $SPLUNK_DB/maindb/colddb
   ls $SPLUNK_DB/maindb/frozendb
   ```
4. Verificar se o Splunk está movendo os buckets para a pasta `frozendb` (ou deletando, se não há script definido).
5. Conferir mensagens nos logs:
   ```spl
   index=_internal source=*splunkd.log "will be frozen"
   ```

**🔧 Correção:**
Editar `indexes.conf` para aumentar o valor de retenção:
```ini
[main]
frozenTimePeriodInSecs = 2592000   # 30 dias
```
Reiniciar Splunk após alteração:
```bash
./splunk restart
```
⚠️ Dados congelados sem `coldToFrozenScript` são apagados permanentemente.

**✅ Resultado Esperado:**
Após aumentar o tempo de retenção, os dados permanecem disponíveis conforme o esperado, respeitando o novo limite.

**💡 Lição Aprendida:**
Buckets são movidos conforme configurações de retenção. Um valor mal ajustado de `frozenTimePeriodInSecs` pode causar perda de dados valiosos.
