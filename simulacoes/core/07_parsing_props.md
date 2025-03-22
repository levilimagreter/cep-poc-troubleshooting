# Simulação Core 07 – Erro de parsing no props.conf (linha malformada)

**🔹 Título:** Linha malformada em `props.conf` impede parsing correto de eventos

**❗ Problema:**
Os eventos de um determinado sourcetype estão sendo ingeridos, mas os campos esperados (como `host`, `source`, `sourcetype`, ou campos extraídos via `EXTRACT`) não aparecem corretamente. Nenhum erro visível na UI.

**🧪 Causa Simulada:**
O `props.conf` possui uma linha malformada (ex: falta de `=` ou uso de sintaxe incorreta), o que impede que a configuração do sourcetype seja aplicada.

**🔍 Passos de Investigação:**
1. Verificar os campos extraídos nos eventos:
   ```spl
   index=main sourcetype=meu_sourcetype | table _time, _raw, campo_esperado
   ```

2. Usar `btool` para verificar parsing do `props.conf`:
   ```bash
   ./splunk btool props list meu_sourcetype --debug
   ```
   → Se houver erro de parsing, o `btool` pode pular a entrada ou apresentar uma quebra na listagem.

3. Validar manualmente o conteúdo do arquivo:
   ```bash
   cat $SPLUNK_HOME/etc/apps/minha_app/local/props.conf
   ```
   Exemplo de erro:
   ```
   [meu_sourcetype]
   LINE_BREAKER ^\n
   SHOULD_LINEMERGE = false
   ```
   (Falta de `=` na diretiva `LINE_BREAKER`)

**🔧 Correção:**
Corrigir a linha malformada no `props.conf`. Exemplo:
```ini
[meu_sourcetype]
LINE_BREAKER = ^\n
SHOULD_LINEMERGE = false
```
Reiniciar o Splunk após a correção:
```bash
./splunk restart
```

**✅ Resultado Esperado:**
Após a correção, o sourcetype é interpretado corretamente e os campos esperados são extraídos.

**💡 Lição Aprendida:**
O Splunk não sempre mostra erro evidente para arquivos malformados. Usar `btool` e revisar manualmente os `.conf` ajuda a identificar rapidamente problemas de sintaxe.
