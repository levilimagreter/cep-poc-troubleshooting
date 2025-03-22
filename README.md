# 📂 Troubleshooting Session – Estrutura do Repositório

Este repositório contém simulações de troubleshooting avançado em ambientes Splunk, organizadas por categoria de uso. Cada cenário é documentado separadamente para facilitar a consulta durante o dia a dia de trabalho técnico.

## 📁 Estrutura de Diretórios

```bash
troubleshooting-session/
├── README.md
├── github/                         # Recursos para publicação no GitHub
├── uteis/                          # Comandos, checklists e extras
├── Simulacoes/
│   ├── core/                       # Problemas gerais do Splunk
│   │   ├── 01-dados-nao-aparecem.md
│   │   ├── 02-timestamp-incorreto.md
│   │   ├── ...                     # Até 30 simulações
│   ├── itsi/                       # Problemas relacionados ao ITSI
│   │   ├── 01-kpi-nulo.md
│   │   ├── ...
│   ├── es/                         # Problemas do Enterprise Security
│   │   ├── 01-notaveis-sumiram.md
│   │   ├── ...
│   ├── infosec/                    # Problemas no app Infosec
│   │   ├── 01-dashboards-vazios.md
│   │   ├── ...
├── Fluxos/                         # Diagramas e fluxos de troubleshooting
│   ├── fluxo-indexacao.md
│   ├── fluxo-kpi-itsi.md
│   ├── fluxo-notaveis-es.md
├── Comandos/                       # Coleção de comandos úteis
│   └── comandos-uteis.md
└── Extras/                         # Checklists, templates e extras
    └── checklists.md
```

## ✅ Objetivo
Organizar e compartilhar simulações de problemas reais que ocorrem em ambientes Splunk com foco em:
- Pensamento consultivo para troubleshooting
- Documentação clara e prática
- Reaproveitamento em treinamentos e workshops

## 📌 Como usar
Cada arquivo `.md` dentro de `Simulacoes/` descreve:
- Descrição do problema
- Causa simulada
- Passos de investigação
- Solução aplicada
- Lição aprendida

> Este repositório serve como guia prático para apresentações, workshops e referência em campo.

---

💬 Sugestões de melhorias ou novos cenários são bem-vindos!
