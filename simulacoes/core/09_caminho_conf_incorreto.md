# Simulação Core 09 – btool revela path incorreto de configuração

**🔹 Título:** `btool` mostra configuração sendo aplicada de local inesperado

**❗ Problema:**
Foi feita uma alteração em um arquivo de configuração (`props.conf`, `transforms.conf`, etc.), mas o Splunk continua aplicando valores antigos. Suspeita-se que a configuração nova não está sendo aplicada.

**🧪 Causa Simulada:**
A alteração foi feita em um local errado (por exemplo, `default/` ao invés de `local/`, ou dentro de outro app). O `btool` mostra que o Splunk está usando uma versão da configuração de outro caminho.

**🔍 Passos de Investigação:**
1. Rodar `btool` para inspecionar a configuração efetiva:
   ```bash
   ./splunk btool props list meu_sourcetype --debug
   ```
   Exemplo de saída:
   ```
   /opt/splunk/etc/apps/Splunk_TA_old/default/props.conf [meu_sourcetype]
   KV_MODE = auto
   ```
   → A configuração em `/local` não aparece, porque está no app errado ou com erro de sintaxe.

2. Validar se a modificação foi salva no local correto:
   - Deve estar em `local/props.conf` dentro do app ativo.

3. Verificar se há duplicidade de apps com nomes semelhantes:
   ```bash
   ls $SPLUNK_HOME/etc/apps/ | grep TA
   ```

**🔧 Correção:**
Mover ou replicar a configuração correta para o caminho certo:
```bash
cp /opt/splunk/etc/apps/app_errado/default/props.conf \
   /opt/splunk/etc/apps/app_correto/local/props.conf
```
Verificar novamente com `btool` e reiniciar Splunk:
```bash
./splunk restart
```

**✅ Resultado Esperado:**
`btool` mostra agora o caminho correto, e o comportamento esperado se aplica aos eventos.

**💡 Lição Aprendida:**
Mesmo quando tudo “parece certo”, o Splunk pode estar lendo um arquivo de outro local. O `btool` com `--debug` é a melhor ferramenta para rastrear configurações efetivas no ambiente.
