<pre>
mermaid
graph TD
  A[Eventos presentes mas painel de correlacao vazio] --> B1[Eventtype aplicado aos eventos?]
  B1 -->|Nao| F1[Revisar definição de eventtype, sourcetype e tags]
  B1 -->|Sim| B2[Tags esperadas estao aplicadas?]
  B2 -->|Nao| F2[Configurar tags via props.conf e TAG-*]
  B2 -->|Sim| B3[Modelo de dados populado corretamente?]
  B3 -->|Nao| F3[Habilitar aceleracao e validar pivots do datamodel]
  B3 -->|Sim| B4[Campos esperados (user, src, action) existem?]
  B4 -->|Nao| F4[Normalizar via EXTRACT/REPORT ou aliases]
  B4 -->|Sim| G[Correlacoes devem aparecer nos paineis do app]

</pre>
