# Simulação Core 24 – Atribuição incorreta do campo `host`

**🔹 Título:** Campo `host` atribuído incorretamente em eventos

**❗ Problema:**
Os eventos estão sendo indexados com o campo `host` incorreto, como o nome do forwarder, nome do arquivo, ou até mesmo um valor genérico. Isso afeta filtros, dashboards e agrupamentos por host.

**🧪 Causa Simulada:**
O Splunk está aplicando uma configuração incorreta de `host_segment`, `set_host`, ou está usando o valor padrão (hostname do forwarder) por falta de definição explícita no `inputs.conf` ou `props.conf`.

**🔍 Passos de Investigação:**
1. Verificar como o `host` está sendo definido:
   ```spl
   index=main | stats count by host
   ```

2. Conferir se o `host` deveria vir do nome do arquivo, path ou do conteúdo do evento

3. Validar configurações em `inputs.conf`:
   ```ini
   [monitor:///var/log/app/*.log]
   host_segment = 3
   ```
   → Define o `host` como o 3º segmento do path

4. Conferir `props.conf` e `transforms.conf` (se usar `TRANSFORMS-set_host`):
   ```ini
   [meu_sourcetype]
   TRANSFORMS-set_host = from_path
   
   [from_path]
   REGEX = .*\/([^\/]+)\.log
   FORMAT = host::$1
   ```

5. Verificar se há sobreposição com `host` vindo do forwarder (`inputs.conf` local vs. indexer override)

**🔧 Correção:**
1. Corrigir `inputs.conf` ou aplicar `host_segment` corretamente
2. Se necessário, criar regra no `props.conf` com `TRANSFORMS-set_host`
3. Reiniciar Splunk ou reindexar (em ambientes de teste)

**✅ Resultado Esperado:**
O campo `host` passa a refletir o valor desejado (nome do app, hostname real, etc.), permitindo buscas e visualizações corretas.

**💡 Lição Aprendida:**
A origem do campo `host` pode variar conforme configuração. Conhecer e controlar esse comportamento é essencial para consistência de dados e dashboards.
