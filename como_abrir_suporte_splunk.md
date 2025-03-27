# Como abrir um caso de suporte Splunk

Abrir um caso de suporte bem estruturado pode economizar tempo e agilizar a resolução de problemas. Este guia apresenta as melhores práticas para fornecer ao Suporte Splunk todas as informações necessárias.

---

## 1. Descreva o problema com clareza

- **Onde ocorre o problema?**
  - Em um *forwarder*, *indexer*, *search head*, *deployment server*?
  - Inclua uma **topologia** simplificada com IPs ou nomes das instâncias envolvidas.

- **Quais elementos estão presentes?**
  - Qual é o **cronograma** ou sequência de ações que leva ao erro?
  - Quais processos estão em execução quando o erro aparece?

- **Comportamento esperado vs. observado**
  - Seja específico: por exemplo, "os eventos estão atrasados em 15 minutos" é melhor que "estão atrasados".

---

## 2. Classifique o tipo de problema

Ajude o suporte a entender a natureza do problema:

- **Problemas de pesquisa**
  - Incluem Splunk Web, dashboards, roles, apps, visualizações, Search Processing Language (SPL)

- **Problemas de back-end**
  - Travamentos, erros no sistema operacional, API REST, SDK, crashes

- **Problemas de configuração**
  - Extrações, entradas (`inputs.conf`), encaminhamento (`outputs.conf`), autenticação, etc.

- **Problemas de desempenho**
  - Lerdeza em buscas, ingestão ou dashboards

---

## 3. Envie arquivos de diagnóstico (diag)

O Splunk **diag** é essencial para investigar problemas e contém:
- Configurações atuais da instância
- Logs recentes (sem dados indexados)

### Como gerar:
```bash
$SPLUNK_HOME/bin/splunk diag
```

> Gere um diag em qualquer instância envolvida (forwarder, indexer, SH, DS).
> Se o problema envolve comunicação entre instâncias, envie o diag de ambas.

### Boas práticas:
- Rotule os arquivos com o hostname e função (ex: `diag_indexer01.tgz`)
- Se houver muitos forwarders, envie apenas um representativo
- Após qualquer recomendação do suporte, envie novo diag para comparação

Documentação:
- https://docs.splunk.com/Documentation/Splunk/latest/Troubleshooting/Generateadiag

---

## 4. Coletando core dumps (crashes)

Em caso de crash, o Suporte Splunk pode solicitar **core dumps**.

### Passos:
```bash
ulimit -c unlimited
splunk restart
```

- Isso permite que o sistema gere arquivos de memória quando processos crasham
- Afeta apenas o shell atual: se iniciar via systemd ou boot, configure no `/etc/security/limits.conf`

### Dicas:
- Com `--nodaemon`, o core dump pode ser gravado no diretório atual
- Sem `--nodaemon`, o local padrão pode ser `/` (raiz), mas depende de `core_pattern`
```bash
cat /proc/sys/kernel/core_pattern
```
- O nome pode ser `core.1234` (1234 = PID)

Documentação:
- https://docs.splunk.com/Documentation/Splunk/latest/Troubleshooting/Enabledebuglogging

---

## 5. Configurações LDAP

Se o problema estiver relacionado a LDAP:

Envie:
- O arquivo: `$SPLUNK_HOME/etc/system/local/authentication.conf`
- LDIF para um grupo e um usuário de teste
- Logs relevantes: `splunkd.log`, `web_service.log` com debug habilitado

---

## 6. Links úteis

- [Gerar um arquivo de diagnóstico (diag)](https://docs.splunk.com/Documentation/Splunk/latest/Troubleshooting/Generateadiag)
- [Como abrir um bom caso de suporte](https://docs.splunk.com/Documentation/Splunk/latest/Troubleshooting/HowtofileagreatSupportcase)
- [PDF oficial: Trabalhando com o Suporte](https://www.splunk.com/en_us/pdfs/support/working-with-support.pdf)
- [Habilitar logs de depuração (debug)](https://docs.splunk.com/Documentation/Splunk/latest/Troubleshooting/Enabledebuglogging)

> Com essas informações organizadas, o suporte poderá ajudá-lo de forma mais rápida e eficiente.