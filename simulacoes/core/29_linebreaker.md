# Simulação Core 29 – Erro de parsing por LINE_BREAKER mal definido

**🔹 Título:** Eventos estão sendo cortados ou unidos incorretamente devido a `LINE_BREAKER` incorreto

**❗ Problema:**
Os eventos estão chegando fragmentados (divididos no meio) ou agrupando várias linhas como um único evento. Isso afeta a visualização, extração de campos e alertas baseados em padrões.

**🧪 Causa Simulada:**
O parâmetro `LINE_BREAKER` foi definido incorretamente no `props.conf`, fazendo com que o Splunk divida ou una linhas de maneira errada.

**🔍 Passos de Investigação:**
1. Verificar visualmente a estrutura dos eventos:
   ```spl
   index=main sourcetype=meu_sourcetype | table _time, _raw
   ```
   → Identificar se eventos parecem truncados ou agrupados

2. Analisar o formato real do log:
   - Exemplo de linha por evento:
     ```
     [2025-03-22 10:30:01] ERROR Something failed here...
     ```

3. Validar `props.conf` aplicado:
   ```ini
   [meu_sourcetype]
   LINE_BREAKER = ([\r\n]+)
   SHOULD_LINEMERGE = false
   ```
   → Exemplo de erro: `LINE_BREAKER = (
)` → não escapa corretamente

4. Testar com `BREAK_ONLY_BEFORE` como alternativa:
   ```ini
   BREAK_ONLY_BEFORE = ^\[
   ```

5. Verificar logs de ingestão:
   ```spl
   index=_internal sourcetype=splunkd component=Aggregator
   ```

**🔧 Correção:**
Corrigir o `LINE_BREAKER` com base no padrão real de início de evento:
```ini
[meu_sourcetype]
SHOULD_LINEMERGE = false
LINE_BREAKER = ([\r\n]+)\[\d{4}-\d{2}-\d{2}
TIME_PREFIX = \[
TIME_FORMAT = %Y-%m-%d %H:%M:%S
``` 

Se o evento sempre começa com `[` ou timestamp:
```ini
BREAK_ONLY_BEFORE = ^\[
``` 

Reindexar em ambiente de teste se necessário para validar.

**✅ Resultado Esperado:**
Os eventos passam a ser separados corretamente, com cada linha correspondente a um único evento no Splunk.

**💡 Lição Aprendida:**
`LINE_BREAKER` define o corte entre eventos. Erros nessa configuração causam impacto direto na legibilidade e utilidade dos dados ingeridos. Use `SHOULD_LINEMERGE=false` e teste em ambiente controlado sempre que possível.
