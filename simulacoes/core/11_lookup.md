# Simulação Core 11 – Lookup não funcionando (permissão ou cabeçalho inválido)

**🔹 Título:** Lookup não retorna dados em buscas, apesar de existir

**❗ Problema:**
Um painel depende de um lookup CSV para enriquecer dados (por exemplo, com nomes de host, regiões, categorias), mas os campos esperados não aparecem nas buscas.

**🧪 Causa Simulada:**
O lookup CSV está mal formatado (sem cabeçalho) ou com permissões incorretas (não visível para a role do usuário).

**🔍 Passos de Investigação:**
1. Tentar buscar diretamente o lookup:
   ```spl
   | inputlookup meu_lookup.csv
   ```
   → Verificar se retorna dados e se os campos estão corretos.

2. Verificar estrutura do CSV no disco:
   ```bash
   cat $SPLUNK_HOME/etc/apps/meu_app/lookups/meu_lookup.csv
   ```
   → Certifique-se de que a primeira linha contém o cabeçalho (nomes das colunas).

3. Validar permissões via metadata:
   ```bash
   cat $SPLUNK_HOME/etc/apps/meu_app/metadata/local.meta
   ```
   Exemplo esperado:
   ```
   [lookups/meu_lookup.csv]
   access = read : [ * ], write : [ admin ]
   export = system
   ```

4. Checar se o lookup está registrado em `transforms.conf` (se for automatch ou external):
   ```bash
   ./splunk btool transforms list meu_lookup --debug
   ```

**🔧 Correção:**
- Corrigir o cabeçalho do CSV se estiver ausente:
   ```csv
   host,regiao,categoria
   srv01,sudeste,produção
   ...
   ```
- Ajustar permissões no `local.meta` para torná-lo acessível:
   ```
   access = read : [ * ], write : [ admin ]
   export = system
   ```
- Reiniciar o Splunk (se necessário) para recarregar os objetos:
   ```bash
   ./splunk restart
   ```

**✅ Resultado Esperado:**
A busca `| inputlookup` passa a retornar os dados corretamente e os painéis que dependem desse enrichment funcionam normalmente.

**💡 Lição Aprendida:**
Lookups são sensíveis a cabeçalhos e permissões. Sempre verifique a estrutura do CSV e o escopo de visibilidade do objeto no app.
