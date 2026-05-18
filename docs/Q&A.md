# Q&A — Perguntas Antecipadas para Defesa do Projeto

> Documento de apoio à apresentação final (_Pitch Day_), com três perguntas técnicas que a equipa antecipa que possam ser feitas, e respostas preparadas para serem comunicadas de forma clara e direta.

---

## Pergunta 1 — Por que rejeitaram o SMOTE se é a técnica mais conhecida para tratar desequilíbrio?

**Resposta rápida:**
Porque testámos, e ficou pior. Em modelos baseados em árvores como o XGBoost, o SMOTE acaba por confundir o modelo em vez de ajudar.

**Resposta com mais detalhe:**

Sabíamos desde o início que o SMOTE é a técnica mais falada para problemas com classes desequilibradas, e por isso decidimos testá-lo a sério. Esta era inclusive uma das nossas Perguntas de Investigação no Milestone 1: será que o SMOTE melhora os resultados face ao tratamento nativo do XGBoost?

A comparação direta foi clara:

| Métrica | XGBoost normal | XGBoost com SMOTE | O que aconteceu |
| :--- | :---: | :---: | :---: |
| Recall | 78,95% | 76,84% | Piorou |
| Precision | 93,75% | 86,90% | Piorou |
| F1-Score | 0.8571 | 0.8156 | Piorou |
| AUPRC | 0.8251 | 0.8044 | Piorou |
| Falsos Positivos | 5 | 11 | Mais do dobro |

**Por que é que isto aconteceu, na prática:**

1. O XGBoost já tem um truque interno para lidar com desequilíbrio. Chama-se scale_pos_weight e basicamente diz ao modelo "cada fraude vale 599 vezes mais do que uma transação normal". Isto é incorporado diretamente no cálculo matemático do modelo, sem precisar de inventar dados novos.

2. O SMOTE inventa fraudes que não existem. Pega em duas fraudes reais e cria pontos artificiais entre elas, por interpolação. Em modelos lineares isto até pode ajudar. Mas as árvores de decisão funcionam de forma diferente: cada árvore tenta separar os dados em grupos puros, e quando lhe metemos pontos artificiais no meio, a árvore acaba por aprender padrões que não correspondem à realidade.

3. O resultado vê-se nos Falsos Positivos: subimos de 5 para 11. Ou seja, o modelo passou a "ver fraudes" em transações legítimas, porque foi treinado em zonas estatísticas que não existem no mundo real.

**Conclusão:**
Embora pareça contraintuitivo, este resultado está bem documentado na literatura sobre _Gradient Boosting_ aplicado a problemas desequilibrados. Não rejeitámos o SMOTE só porque sim, testámo-lo, comparámos os resultados e tomámos a decisão baseada nos dados. Esta era exatamente uma das perguntas que tínhamos definido para investigar.

---

## Pergunta 2 — Por que escolheram o XGBoost simples em vez do XGBoost otimizado, que apanha mais fraudes?

**Resposta rápida:**
Porque o modelo otimizado apanha um pouco mais de fraudes, mas para isso bloqueia muito mais clientes legítimos por engano. Em termos práticos para um banco, é completamente inaceitável.

**Resposta com mais detalhe:**

Comparámos os dois modelos lado a lado:

| Métrica | XGBoost Simples | XGBoost Otimizado | Diferença |
| :--- | :---: | :---: | :---: |
| AUPRC | 0.8251 | 0.7936 | Pior no otimizado |
| Recall | 78,95% | 83,16% | +4 no otimizado |
| Precision | 93,75% | 44,89% | -49 no otimizado** |
| Falsos Positivos | 5 | 97 | +1840% |

**O que isto significa na prática:**

Para detetar 4 fraudes adicionais, o modelo otimizado bloqueia indevidamente mais 92 clientes legítimos. Ou seja, para cada fraude extra apanhada, há 23 clientes a serem incomodados sem razão. Para um banco, isto é um pesadelo operacional: imensos clientes a ligar para a linha de apoio, perda de confiança, equipas sobrecarregadas.

**A nossa decisão baseou-se em três fatores:**

1. **Desempenho global** — o modelo simples ganha em quase todas as métricas (AUPRC, Precision, F1-Score). Só perde em Recall isolado.

2. **Estabilidade** — quando validámos com Cross-Validation (K=5), o modelo simples mostrou variação menor entre dobras, o que indica que é mais estável.

3. **Interpretabilidade** — parâmetros simples como n_estimators=100 e max_depth=6 são muito mais fáceis de explicar a alguém da gestão do que uma configuração complexa de uma pesquisa aleatória.

**Sobre o critério SMART:**

O nosso critério SMART exige cumprir três métricas em simultâneo: Recall ≥ 75%, Precision ≥ 85% e AUPRC ≥ 0.80. O modelo simples cumpre os três. O otimizado falha redondamente na Precision (44,89% quando o limiar é 85%). Portanto, a escolha é clara.

**Conclusão:**
Em deteção de fraude bancária, é melhor detetar 79% das fraudes com 94% de confiança do que detetar 83% com apenas 45% de confiança. Confiança elevada permite bloquear automaticamente; confiança baixa obriga a verificação humana de cada alerta, multiplicando os custos operacionais e o atrito com clientes.

---

## Pergunta 3 — Por que escolheram um critério SMART com três métricas em vez de uma única?

**Resposta rápida:**
Porque uma métrica isolada pode ser facilmente "manipulada", basta empurrar o modelo para um extremo. Ao exigir três métricas em simultâneo, garantimos que o modelo final é equilibrado e funciona no mundo real, não só no papel.

**Resposta com mais detalhe:**

Desde o Milestone 1 definimos um critério SMART composto:

> **Recall ≥ 75% E Precision ≥ 85% E AUPRC ≥ 0.80**

A razão para esta escolha é simples: se tivéssemos definido apenas "Recall ≥ 85%", como inicialmente se pensa, seria possível atingir essa métrica destruindo todas as outras. E foi exatamente isso que a experimentação mostrou, o modelo Tuned consegue um Recall altíssimo, mas com uma Precision desastrosa.

**O que a experimentação confirmou:**

| Modelo testado | Recall | Precision | Falsos Positivos | Cumpre SMART? |
| :--- | :---: | :---: | :---: | :---: |
| Regressão Logística | 57,89% | 87,30% | 8 | Não, Recall baixo |
| XGBoost Simples | 78,95% | 93,75% | 5 | Sim |
| XGBoost + SMOTE | 76,84% | 86,90% | 11 | Sim, mas pior em tudo |
| XGBoost Otimizado | 83,16% | 44,89% | 97 | Não, Precision insustentável |

Repare-se que o XGBoost Otimizado tem o melhor Recall isolado, mas é precisamente o pior em termos de utilidade prática. Se tivéssemos um critério baseado só em Recall, este seria o "vencedor". Com o critério composto, fica claro que não cumpre as condições essenciais.

**Os três pontos fortes desta abordagem:**

1. **É matematicamente exigente.** Não basta brilhar numa métrica, é preciso cumprir as três em simultâneo. Isto elimina otimizações cosméticas.

2. **Reflete a realidade do banco.** Em produção real, há dois riscos: deixar fraudes escapar (Recall baixo) ou bloquear clientes inocentes (Precision baixa). O critério composto captura ambos.

3. **É a prática reconhecida na área.** A literatura científica em deteção de fraude usa exatamente esta abordagem, métricas compostas em vez de isoladas.

**O nosso modelo final cumpre os três critérios:**

| Critério SMART | Limiar | Resultado obtido |
| :--- | :---: | :---: |
| AUPRC | ≥ 0.80 | 0.8251 |
| Recall | ≥ 0.75 | 0.7895 |
| Precision | ≥ 0.85 | 0.9375 |

**Conclusão:**
A escolha de um critério SMART composto não foi um detalhe técnico, foi uma decisão metodológica. Quisemos garantir que o modelo final não funcionasse apenas "no papel", mas que tivesse equilíbrio real entre deteção e qualidade. É essa a postura que o Milestone 4 nos pede: menos números isolados, mais interpretação do conjunto.

---

## Notas Finais

* Estas três respostas estão alinhadas com a documentação técnica completa em [docs/M3_modelacao.md](M3_modelacao.md) e [docs/M4_conclusoes.md](M4_conclusoes.md).
* Toda a evidência empírica apresentada é reproduzível executando o notebook [notebooks/2.0_modelacao_treino.ipynb](../notebooks/2.0_modelacao_treino.ipynb).

---

*Documento preparado pela equipa do Grupo 8 como complemento à defesa final do projeto.*
*Coimbra Business School | ISCAC — Ano Letivo 2025/2026*
