# Simulação Core 08 – Configuração duplicada em `default` e `local`

**🔹 Título:** Comportamento inesperado causado por conflito entre configurações em `default` e `local`

**❗ Problema:**
Mesmo após configurar corretamente um parâmetro em `local/props.conf`, o Splunk continua aplicando uma configuração incorreta. O comportamento esperado não se reflete nos eventos.

**🧪 Causa Simulada:**
Existe uma configuração conflitante para o mesmo sourcetype em dois arquivos diferentes (ex: `default/props.conf` e `local/props.conf`), e o parâmetro no `default` tem precedência por estar em outro app com maior prioridade.

**🔍 Passos de Investigação:**
1. Verificar o valor aplicado com `btool`:
   ```bash
   ./splunk btool props list meu_sourcetype --debug
   ```
   Exemplo de saída:
   ```
   /opt/splunk/etc/apps/Splunk_TA_example/default/props.conf [meu_sourcetype]
   TIME_FORMAT = %d/%m/%Y %H:%M:%S
   /opt/splunk/etc/apps/minha_app/local/props.conf [meu_sourcetype]
   TIME_FORMAT = %Y-%m-%d %H:%M:%S
   ```
   → O Splunk está aplicando a versão do TA, que tem prioridade mais alta.

2. Verificar a ordem de precedência entre apps:
   - Apps com `default` podem sobrescrever outros com `local` dependendo da hierarquia de contexto e escopo (system > app > user).

3. Conferir as permissões e escopos definidos no `app.conf`:
   ```bash
   cat $SPLUNK_HOME/etc/apps/Splunk_TA_example/metadata/default.meta
   ```

**🔧 Correção:**
Mover a configuração para um app com precedência mais alta ou ajustar diretamente no TA (se for controlado por você).

Outra opção: desabilitar o TA conflituoso se não for necessário.

**✅ Resultado Esperado:**
Após resolver o conflito, o Splunk aplica corretamente a configuração esperada.

**💡 Lição Aprendida:**
A hierarquia de configuração no Splunk é poderosa, mas pode causar efeitos colaterais. O `btool` é fundamental para entender exatamente qual arquivo está sendo usado e de onde vem cada parâmetro.
