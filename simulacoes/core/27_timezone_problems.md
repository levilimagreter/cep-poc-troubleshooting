# Simulação Core 27 – Problema de timezone causando atraso em alertas

**🔹 Título:** Alerta executa com atraso ou fora da janela esperada devido a timezone incorreto

**❗ Problema:**
Um alerta ou painel está exibindo dados com horário incorreto (adiantado ou atrasado). O `_time` dos eventos não reflete o fuso horário da localidade.

**🧪 Causa Simulada:**
A configuração de timezone (`TZ`) não está corretamente aplicada no `props.conf`, ou os eventos têm timestamps sem offset. Como resultado, o Splunk assume UTC por padrão ou aplica o fuso horário do sistema.

**🔍 Passos de Investigação:**
1. Verificar os timestamps dos eventos:
   ```spl
   index=main | eval hora_evento=strftime(_time, "%Y-%m-%d %H:%M:%S") | table _time, hora_evento, _raw
   ```
   → Verificar se `_time` está deslocado da hora real

2. Validar se o timezone está explícito nos eventos (`+00:00`, `-0300`, etc)

3. Verificar configuração de `TZ` no `props.conf`:
   ```ini
   [meu_sourcetype]
   TZ = America/Sao_Paulo
   ```

4. Conferir a timezone do sistema operacional (Search Head ou Heavy Forwarder):
   ```bash
   timedatectl
   ```

5. Validar se há `DATETIME_CONFIG = NONE`, o que força `_time = _indextime`

**🔧 Correção:**
1. Aplicar o parâmetro correto no `props.conf`:
   ```ini
   [meu_sourcetype]
   TZ = America/Sao_Paulo
   ```

2. Reiniciar o Splunk após alteração:
   ```bash
   ./splunk restart
   ```

3. Reindexar dados em ambiente de teste para validar timezone corrigido

**✅ Resultado Esperado:**
Os eventos passam a ter `_time` com fuso horário correto, alinhando alertas, painéis e correlações temporais.

**💡 Lição Aprendida:**
O timezone deve ser tratado com cuidado na ingestão. O parâmetro `TZ` em `props.conf` é essencial quando os eventos não contêm offset explícito.
