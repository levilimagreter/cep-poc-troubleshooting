# Simulação Core 25 – Campo `source` com caminho inesperado (symlink ou glob mal definido)

**🔹 Título:** Campo `source` aparece com caminho estranho ou inconsistente nos eventos

**❗ Problema:**
Nos eventos ingeridos, o campo `source` mostra caminhos inesperados (ex: `/proc/xyz`, `/tmp/symlink/../log.log`, ou com duplicação de diretórios). Isso prejudica agrupamentos, filtros e dashboards baseados nesse campo.

**🧪 Causa Simulada:**
O Splunk está monitorando arquivos via symlink, glob (`*` ou `...`) mal definido, ou com caminhos relativos que geram valores inconsistentes no campo `source`.

**🔍 Passos de Investigação:**
1. Visualizar exemplos do campo `source`:
   ```spl
   index=main | stats count by source
   ```
   → Verificar se os caminhos parecem quebrados, duplicados ou inconsistentes

2. Analisar `inputs.conf`:
   ```ini
   [monitor:///var/log/app/*.log]
   disabled = false
   ```
   Ou:
   ```ini
   [monitor:///var/log/app/.../access.log]
   followSymlink = true
   ```

3. Verificar se o caminho monitorado usa links simbólicos:
   ```bash
   ls -l /var/log/app
   ```
   → Ex: `access.log -> /mnt/shared/logs/access.log`

4. Validar comportamento do Splunk com globs e symlinks:
   - Symlinks podem ser seguidos se `followSymlink = true`
   - Pode gerar paths como `/tmp/symlink/../realpath.log`

**🔧 Correção:**
1. Evitar monitorar diretórios via symlink quando possível
2. Utilizar caminho absoluto e direto para o arquivo real
3. Corrigir padrão `glob` para evitar ambiguidade
4. Se necessário, sobrescrever o campo `source` com `TRANSFORMS-set_source`:
   ```ini
   [meu_sourcetype]
   TRANSFORMS-fix_source = normalize_source

   [normalize_source]
   REGEX = .*/logs/(.*)
   FORMAT = source::log/$1
   ```

**✅ Resultado Esperado:**
O campo `source` passa a conter valores consistentes, facilitando visualização, filtros e agrupamentos em dashboards.

**💡 Lição Aprendida:**
O Splunk gera o campo `source` a partir do caminho real do arquivo monitorado. Usar symlinks ou padrões genéricos pode comprometer a padronização. Preferir caminhos absolutos e normalização com `transforms` quando necessário.
