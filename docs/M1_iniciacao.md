# Milestone 1: Iniciação e Definição do Projeto

## 1. Descrição Detalhada do Problema
O setor bancário enfrenta um desafio contínuo com a fraude em cartões de crédito, que resulta em prejuízos financeiros diretos para as instituições e perda de confiança por parte dos consumidores. O problema central deste projeto não é apenas detetar a fraude, mas fazê-lo num cenário de **extremo desequilíbrio**: a grande maioria das transações é legítima.

Neste contexto, um modelo que simplesmente classifique todas as transações como "legítimas" teria uma precisão de 99.9%, mas falharia completamente o objetivo de negócio (detetar a fraude). O desafio de gestão reside no *trade-off* entre bloquear transações fraudulentas (evitar Falsos Negativos - perda de dinheiro) e não bloquear transações legítimas (evitar Falsos Positivos - insatisfação do cliente).

O dataset selecionado contém transações efetuadas por cartões de crédito europeus em setembro de 2013, onde as variáveis de entrada foram anonimizadas através de uma transformação PCA (Principal Component Analysis) para proteção de dados sensíveis.

## 2. Objetivos SMART
1.  **Objetivo 1:** Atingir uma métrica AUPRC superior a 0.80 na identificação de fraudes, utilizando técnicas de oversampling (como SMOTE) para contornar o desequilíbrio de classes, até ao final do semestre.
2.  **Objetivo 2:** Identificar as 5 variáveis (entre as componentes V1-V28, Time e Amount) com maior poder preditivo na deteção de fraude através de técnicas de Feature Importance, até à entrega da fase de modelação (Milestone 3).

## 3. Metodologia de Gestão (PBL)
* **Divisão de Tarefas:**
    * **João Freire:** Modelação e Engenharia de Dados.
    * **Rodrigo Ferrão:** Análise Exploratória e Visualização.
    * **Tomás Moreira:** Gestão do Repositório e Documentação.
* **Ferramentas de Colaboração:**
    * **Gestão de Tarefas:** GitHub Projects (Kanban: *To Do, In Progress, Done*).
    * **Comunicação:** Grupo dedicado no WhatsApp/Discord para *dailies* rápidas.
    * **Controlo de Versão:** Git (Branching strategy: `main` para produção, branches individuais para desenvolvimento).

## 4. Análise de Viabilidade dos Dados
* **Disponibilidade:** Os dados foram obtidos via Kaggle (*Credit Card Fraud Detection*) e já se encontram no repositório na pasta `data/raw/`.
* **Qualidade Inicial:** O dataset é considerado de "alta qualidade" técnica (limpo), com 284.807 registos. Não existem valores nulos (NaN), o que acelera a fase de limpeza.
* **Ética:**
    * Os dados cumprem rigorosos padrões de privacidade. As variáveis `V1` a `V28` são o resultado de uma transformação PCA, garantindo que não temos acesso a dados pessoais identificáveis (PII) dos utilizadores, o que elimina barreiras éticas no tratamento da informação.
    * As únicas variáveis não transformadas são `Time` e `Amount`.

## 5. Cronograma Interno
| Fase | Data Limite | Entregável Esperado |
| :--- | :--- | :--- |
| M1: Iniciação | 24/02/2026 | Repositório estruturado e Plano de Projeto. |
| M2: Exploração | [Data] | Notebook de EDA e Dados Processados. |
| M3: Modelação | [Data] | Comparação de algoritmos e métricas. |
| M4: Finalização| [Data] | Pitch e Relatório Final. |
---
*Data de última atualização: [20/02/2026]*
