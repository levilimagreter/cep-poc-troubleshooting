# Simulação Core 12 – Campo extraído incorretamente via props.conf

**🔹 Título:** Campo calculado não aparece devido a erro de expressão regular em `props.conf`

**❗ Problema:**
Eventos estão sendo ingeridos normalmente, mas um campo esperado (como `user`, `ip`, ou `status`) não é extraído automaticamente como deveria.

**🧪 Causa Simulada:**
O campo foi configurado via `EXTRACT-` no `props.conf`, mas a expressão regular está incorreta ou não compatível com o formato real dos logs.

**🔍 Passos de Investigação:**
1. Verificar se o campo aparece nos eventos:
   ```spl
   index=main sourcetype=meu_sourcetype | table _time, _raw, user
   ```

2. Validar se existe uma definição de extração via `props.conf`:
   ```bash
   ./splunk btool props list meu_sourcetype --debug | grep EXTRACT
   ```

3. Analisar a expressão regular definida:
   ```ini
   EXTRACT-user = user:\s(?<user>\w+)
   ```

4. Testar manualmente a regex com `rex` no Search:
   ```spl
   index=main sourcetype=meu_sourcetype
   | rex field=_raw "user:\s(?<user>\w+)"
   | table user, _raw
   ```
   → Verificar se a regex realmente encontra o valor esperado

5. Usar regex tester (como regex101) com exemplo real de evento

**🔧 Correção:**
Ajustar a regex no `props.conf` para refletir corretamente o padrão dos eventos. Exemplo:
```ini
[meu_sourcetype]
EXTRACT-user = user="(?<user>[^"]+)"
```
Reiniciar Splunk:
```bash
./splunk restart
```

**✅ Resultado Esperado:**
O campo passa a ser extraído automaticamente para o sourcetype, aparecendo nas buscas e painéis.

**💡 Lição Aprendida:**
Extrações no `props.conf` devem ser validadas com exemplos reais. Sempre teste sua regex manualmente antes de aplicar em produção.
