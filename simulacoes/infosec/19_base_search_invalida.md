# Simulação Infosec – 19 base search invalida

**🔹 Título:** 19 base search invalida

**❗ Problema:**
Descrição clara do problema relacionado ao uso do app Infosec.

**🧪 Causa Simulada:**
Explicação técnica da causa, como ausência de tag, macro malformada, index incorreto, etc.

**🔍 Passos de Investigação:**
1. Buscar no `index=*` e verificar dados brutos
2. Avaliar tags e campos esperados com `tag=*` ou `sourcetype=*`
3. Testar macros envolvidas no painel/alerta
4. Usar `btool`, `_internal`, `rest`, e `inputlookup` conforme o caso

**🔧 Correção:**
- Ajustar configuração de sourcetype, macro, ou lookup
- Corrigir permissões
- Garantir que os eventos sejam compatíveis com as buscas do app

**✅ Resultado Esperado:**
Dashboards, alertas e buscas funcionam corretamente com base nos dados indexados.

**💡 Lição Aprendida:**
Reforçar boas práticas de configuração, visibilidade e validação de objetos no contexto do Infosec App.
