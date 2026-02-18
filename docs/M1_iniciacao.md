# Milestone 1: Iniciação e Definição do Projeto

## 1. Descrição Detalhada do Problema
O setor bancário enfrenta um desafio contínuo com a fraude em cartões de crédito, que resulta em prejuízos financeiros diretos para as instituições e perda de confiança por parte dos consumidores. O problema central deste projeto não é apenas detetar a fraude, mas fazê-lo num cenário de **extremo desequilíbrio**: a grande maioria das transações é legítima.

Neste contexto, um modelo que simplesmente classifique todas as transações como "legítimas" teria uma precisão de 99.9%, mas falharia completamente o objetivo de negócio (detetar a fraude). O desafio de gestão reside no *trade-off* entre bloquear transações fraudulentas (evitar Falsos Negativos - perda de dinheiro) e não bloquear transações legítimas (evitar Falsos Positivos - insatisfação do cliente).

O dataset selecionado contém transações efetuadas por cartões de crédito europeus em setembro de 2013, onde as variáveis de entrada foram anonimizadas através de uma transformação PCA (Principal Component Analysis) para proteção de dados sensíveis.

## 2. Objetivos SMART

1.  **Objetivo Técnico (S/M):** Desenvolver um classificador binário capaz de atingir uma **AUPRC (Area Under the Precision-Recall Curve)** superior a **0.80** no conjunto de teste, superando a performance de modelos de base (dummy classifiers).
2.  **Objetivo Metodológico (A):** Implementar e validar a eficácia da técnica **SMOTE (Synthetic Minority Over-sampling Technique)** para corrigir o desequilíbrio de classes (0.172% fraudes) no conjunto de treino.
3.  **Objetivo de Negócio (R):** Identificar quais as variáveis comportamentais (ex: `Time` ou `Amount`) têm maior correlação com a fraude, produzindo um relatório de *insights* para a gestão de risco.
4.  **Objetivo Temporal (T):** Ter o protótipo do modelo validado, com o relatório de custos de erro (Matriz de Confusão Financeira), entregue até ao final do semestre letivo.

## 3. Metodologia de Gestão (PBL)
* **Divisão de Tarefas (Papéis Iniciais):**
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
* **Ética e Privacidade:**
    * Os dados cumprem rigorosos padrões de privacidade. As variáveis `V1` a `V28` são o resultado de uma transformação PCA, garantindo que não temos acesso a dados pessoais identificáveis (PII) dos utilizadores, o que elimina barreiras éticas no tratamento da informação.
    * As únicas variáveis não transformadas são `Time` e `Amount`.

## 5. Cronograma Interno
| Fase | Data Limite | Entregável Esperado |
| :--- | :--- | :--- |
| M1: Iniciação | [Data] | Repositório estruturado e Plano de Projeto. |
| M2: Exploração | [Data] | Notebook de EDA e Dados Processados. |
| M3: Modelação | [Data] | Comparação de algoritmos e métricas. |
| M4: Finalização| [Data] | Pitch e Relatório Final. |
---
*Data de última atualização: [DD/MM/AAAA]*
