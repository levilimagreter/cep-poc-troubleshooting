# Simulação Core 26 – Arquivo de configuração corrompido (invalid stanza)

**🔹 Título:** Splunk ignora configurações por erro de sintaxe em arquivo `.conf`

**❗ Problema:**
Configurações feitas recentemente não estão surtindo efeito. O Splunk não está aplicando parâmetros definidos em arquivos como `inputs.conf`, `props.conf` ou `transforms.conf`.

**🧪 Causa Simulada:**
Há um erro de sintaxe (ex: colchete mal fechado, chave duplicada, parâmetro inválido) que torna uma stanza inválida. O Splunk ignora o bloco e não aplica as configurações.

**🔍 Passos de Investigação:**
1. Validar o conteúdo do arquivo alterado (ex: `props.conf`):
   ```ini
   [meu_sourcetype
   SHOULD_LINEMERGE = false
   ```
   → Está faltando o colchete de fechamento

2. Usar `btool` para identificar falhas:
   ```bash
   ./splunk btool props list meu_sourcetype --debug
   ```
   → Se a stanza estiver ausente, ela foi ignorada

3. Consultar `splunkd.log` para mensagens de parsing:
   ```spl
   index=_internal source=*splunkd.log "invalid stanza"
   ```
   → Erros como: `Invalid stanza [meu_sourcetype` found in props.conf`

4. Conferir duplicações de parâmetros ou seções sobrepostas

**🔧 Correção:**
1. Corrigir a sintaxe no arquivo `.conf`:
   - Fechar colchetes
   - Remover parâmetros inválidos
   - Evitar duplicações em stanzas com mesmo nome

2. Validar novamente com `btool` após correção

3. Reiniciar o Splunk para garantir recarregamento:
   ```bash
   ./splunk restart
   ```

**✅ Resultado Esperado:**
As configurações passam a ser aplicadas corretamente e refletem no comportamento dos dados ingeridos ou buscas.

**💡 Lição Aprendida:**
Erros de sintaxe em arquivos `.conf` não geram erro de sistema, mas causam ignorância silenciosa das configurações. `btool` e `splunkd.log` são indispensáveis para validar arquivos após edição.

