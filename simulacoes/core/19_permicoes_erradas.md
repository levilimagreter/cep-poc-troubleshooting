# Simulação Core 19 – Permissões inadequadas em roles

**🔹 Título:** Usuário não visualiza dados ou objetos esperados por falta de permissões na role

**❗ Problema:**
Um usuário com acesso ao Splunk não consegue visualizar determinados dados, dashboards, ou campos. Outros usuários visualizam normalmente.

**🧪 Causa Simulada:**
A role atribuída ao usuário não possui permissões adequadas para acessar o index, sourcetype ou knowledge objects usados nos painéis e buscas.

**🔍 Passos de Investigação:**
1. Confirmar o comportamento com o usuário afetado:
   - Dashboards vazios
   - Campos ausentes
   - Busca sem resultados, mesmo sem erro

2. Verificar a role atribuída ao usuário:
   - *Settings → Roles → [Nome da Role]*
   - Validar:
     - Indexes permitidos (`Indexes searched by default` e `Indexes allowed`)
     - Capabilities (ex: `search`, `schedule_search`, `accelerate_datamodel`)

3. Validar objetos de conhecimento:
   - Dashboards: restritos por app ou escopo
   - Lookups: visíveis apenas para `admin`
   - Saved searches: não compartilhadas ou com escopo limitado

4. Conferir metadados de permissões:
   ```bash
   cat $SPLUNK_HOME/etc/apps/minha_app/metadata/local.meta
   ```
   Exemplo:
   ```
   [views/painel_customizado]
   access = read : [ admin ], write : [ admin ]
   ```

**🔧 Correção:**
1. Expandir os indexes e capabilities da role:
   - Adicionar o index necessário a `Indexes allowed`
   - Incluir capabilities ausentes (se aplicável)

2. Alterar permissões dos objetos compartilhados:
   - Acessar via UI → *Permissions* → Compartilhar com app ou todos os usuários
   - Ajustar via `local.meta`:
     ```
     access = read : [ * ], write : [ admin ]
     export = system
     ```

**✅ Resultado Esperado:**
Usuário passa a visualizar corretamente os dados, dashboards e objetos esperados com sua role atualizada.

**💡 Lição Aprendida:**
No Splunk, permissões são controladas por roles e escopos de objetos. Garantir visibilidade correta exige revisar tanto roles quanto o escopo dos objetos (dashboards, lookups, etc).

