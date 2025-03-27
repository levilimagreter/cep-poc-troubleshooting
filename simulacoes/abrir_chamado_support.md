# Como abrir um caso de suporte

Ao entrar em contato com o suporte, você pode economizar tempo fornecendo todas as informações relevantes. Aqui estão algumas ideias para você começar.

Descreva o problema
Onde o problema ocorre? Em um encaminhador? Em um indexador? O ideal é compartilhar um topologia com IPs por exemplo.

Quais elementos estão presentes para o problema? Qual é o cronograma que leva ao erro? Quais processos estão em execução quando o erro aparece?

Qual comportamento você observa, em comparação com o que você espera? Seja específico: por exemplo, quão tarde é "atrasado"?

Tente classificar o problema:

É um problema de pesquisa? Isso inclui Splunk Web, gerenciamento, funções, aplicativos, visualizações e painéis, idioma de pesquisa.
É um problema de back-end? Esses problemas podem incluir travamento, problemas de sistema operacional, API REST ou SDK.
É um problema de configuração? Isso inclui extrações, configurações de entrada, encaminhamento, desativação de aplicativos ou autenticação.
É um problema de desempenho?
Enviar arquivos de diagnóstico
A maioria dos casos de suporte são para problemas funcionais: o software foi configurado para fazer algo, mas está se comportando de forma inesperada. O suporte do Splunk precisa do contexto do problema e de insights sobre a instância que não está funcionando como esperado. Esses insights vêm na forma de um "diag" ou arquivo de diagnóstico, que é essencialmente um instantâneo da configuração da instância da plataforma Splunk e dos logs recentes dessa instância.

Você pode fazer um diag em qualquer tipo de instância: forwarder, indexador, search head ou deployment server. Se você tiver um forwarder e um receptor que não estejam funcionando corretamente juntos, envie-nos diags de ambos. Rotule os diags para que fique claro de qual instância cada um é. Se você tiver muitos encaminhadores, envie apenas um diag de encaminhador representativo.

**O tarball diag não contém nenhum dos seus dados indexados**, mas você pode examinar seu conteúdo antes de enviá-lo. Leia sobre o que você pode incluir ou excluir dos diags em Gerar um arquivo de diagnóstico neste manual.

O Suporte Splunk pode solicitar outro diag após recomendar uma alteração ou atualização para a instância. Este diag pode verificar se a alteração foi aplicada e examinar seu efeito. Não é incomum ter vários diags atualizados para um único caso. Se você enviar vários diags, rotule cada um claramente.

Coletando arquivos principais
O Suporte Splunk pode solicitar que você colete um arquivo principal.

Comece definindo o ulimit para remover qualquer configuração de tamanho máximo de arquivo e reinicie o Splunk.

# ulimit -c unlimited
# splunk restart

Esta configuração afeta apenas os processos que você inicia a partir do shell onde executou o comando ulimit. Para descobrir onde os arquivos principais chegam em seu sabor e versão UNIX específicos, consulte a documentação do sistema. O texto abaixo inclui algumas regras gerais que podem ou não se aplicar.

No UNIX, se você iniciar o Splunk com a opção --nodaemon (splunk start --nodaemon), ele pode gravar o arquivo principal no diretório atual. Sem o sinalizador, o local esperado é / (a ​​raiz da árvore do sistema de arquivos). No entanto, várias plataformas têm várias regras sobre onde os arquivos principais vão com ou sem essa configuração. Consulte a documentação do seu sistema. Se você iniciar o splunk com --nodaemon, precisará, em outro shell, iniciar a interface da web manualmente com splunk start splunkweb.

Dependendo do seu sistema, o núcleo pode ser nomeado algo como core.1234, onde '1234' é o ID do processo do programa que está travando.

Trabalhando com configurações LDAP
Se você estiver tendo problemas para configurar o LDAP, o Suporte normalmente precisará das seguintes informações:

O arquivo authentication.conf de $SPLUNK_HOME/etc/system/local/.
Um ldif para um grupo para o qual você está tentando mapear funções.
Um ldif para um usuário como o qual você está tentando se autenticar.
Em alguns casos, um splunkd.log ou web_service.log de depuração é útil.

Splunk Doc:

https://docs.splunk.com/Documentation/Splunk/latest/Troubleshooting/Generateadiag
https://docs.splunk.com/Documentation/Splunk/latest/Troubleshooting/HowtofileagreatSupportcase
https://www.splunk.com/en_us/pdfs/support/working-with-support.pdf
https://docs.splunk.com/Documentation/Splunk/latest/Troubleshooting/Enabledebuglogging

