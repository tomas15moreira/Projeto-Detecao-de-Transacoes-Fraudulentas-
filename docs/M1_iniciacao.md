# Milestone 1: Iniciação e Definição do Projeto

## 1. Descrição Detalhada do Problema
O setor bancário enfrenta um desafio contínuo com a fraude em cartões de crédito, que resulta em prejuízos financeiros diretos para as instituições e perda de confiança por parte dos consumidores. O problema central deste projeto não é apenas detetar a fraude, mas fazê-lo num cenário de extremo desequilíbrio: a grande maioria das transações é legítima.

Neste contexto, um modelo que simplesmente classifique todas as transações como "legítimas" teria uma precisão de 99.9%, mas falharia completamente o objetivo de negócio (detetar a fraude). O desafio de gestão reside no _trade-off_ entre bloquear transações fraudulentas (evitar Falsos Negativos - perda de dinheiro) e não bloquear transações legítimas (evitar Falsos Positivos - insatisfação do cliente).

O _dataset_ selecionado contém transações efetuadas por cartões de crédito europeus em setembro de 2013, onde as variáveis de entrada foram anonimizadas através de uma transformação PCA (_Principal Component Analysis_) para proteção de dados sensíveis.

## 2. Objetivos SMART
1.  **Objetivo 1:** Desenvolver um modelo de classificação para identificar transações fraudulentas em cartões de crédito com uma métrica AUPRC mínima de 0.80, utilizando o _dataset_ disponibilizado no Kaggle, para ser validado e apresentado no _Milestone 3_.
2.  **Objetivo 2:** Aplicar a técnica SMOTE (_oversampling_) para corrigir o desequilíbrio extremo dos dados, treinando um algoritmo que atinja uma Sensibilidade (_Recall_) superior a 85%, garantindo a minimização de perdas financeiras (falsos negativos), até à entrega final do projeto.

**Perguntas de investigação** 
1. Existe uma correlação direta entre o montante da transação e a probabilidade de esta ser classificada como fraude, ou as fraudes tendem a ocorrer em valores mais baixos para passar despercebidas?
2. Quais são as 3 variáveis que mais contribuem para a previsão correta de uma transação ilícita?
3. Existem padrões temporais específicos que sejam mais comuns nas transações fraudulentas comparativamente às transações legítimas?

## 3. Metodologia de Gestão (PBL)
* **Divisão de Tarefas:**
    * **João Freire:** Modelação e Engenharia de Dados.
    * **Rodrigo Ferrão:** Análise Exploratória e Visualização.
    * **Tomás Moreira:** Gestão do Repositório e Documentação.
* **Ferramentas de Colaboração:**
    * **Gestão de Tarefas:** GitHub Projects.
    * **Comunicação:** Grupo dedicado no WhatsApp/Discord para _dailies_ rápidas.
    * **Controlo de Versão:** Git (Branching strategy: _main_ para produção, branches individuais para desenvolvimento).

## 4. Análise de Viabilidade dos Dados
* **Disponibilidade:** Os dados foram obtidos via Kaggle (_Credit Card Fraud Detection_) e já se encontram no repositório na pasta _data/raw/_.
* **Qualidade Inicial:** O _dataset_ é considerado de "alta qualidade" técnica (limpo), com 284.807 registos. Não existem valores nulos (NaN), o que acelera a fase de limpeza.
* **Ética:**
    * Os dados cumprem rigorosos padrões de privacidade. As variáveis V1 a V28 são o resultado de uma transformação PCA, garantindo que não temos acesso a dados pessoais identificáveis (PII) dos utilizadores, o que elimina barreiras éticas no tratamento da informação.
    * As únicas variáveis não transformadas são _Time_ e _Amount_.

## 5. Dicionário de Dados 
O dataset utilizado neste projeto contém transações efetuadas por titulares de cartões de crédito. Uma particularidade técnica fundamental deste conjunto de dados é o seu elevado nível de anonimização. Para proteger a privacidade dos utilizadores e os dados sensíveis das operações, as características originais das transações foram submetidas a uma Análise de Componentes Principais (PCA). 
Como resultado, a maioria das variáveis explicativas (V1 a V28) consiste em componentes numéricas contínuas cuja identidade original foi ocultada, restando apenas o tempo e o montante como variáveis ativas diretamente interpretáveis.

| Variável | Tipo de Dados | Descrição Principal |
| :--- | :--- | :--- |
| **Time** | Numérico Contínuo | Número de segundos decorridos entre a transação atual e a primeira transação registada no _dataset_. Útil para análise de padrões temporais. |
| **V1 a V28** | Numérico Contínuo | 28 variáveis resultantes de uma transformação de dimensionalidade PCA. Os dados originais foram ocultados para cumprir normas de privacidade bancária. |
| **Amount** | Numérico Contínuo | Valor monetário da transação. Esta variável mantém a sua escala original (não sofreu transformação PCA). |
| **Class** | Categórico Binário | Variável alvo. O valor 1 indica uma transação fraudulenta e o valor 0 indica uma transação legítima. |

## 6. Descrição Técnica 
Após a importação e verificação inicial do ficheiro _creditcardfraud.csv_, procedeu-se à análise da estrutura técnica do conjunto de dados. O _dataset_ apresenta uma dimensão considerável, sendo composto por exatamente 284.807 registos de transações distribuídos por 31 colunas distintas. Em termos de integridade, a base de dados destaca-se por estar perfeitamente preenchida, não apresentando qualquer valor nulo (NaN) ou ausente em toda a sua extensão, o que atesta a sua fiabilidade e dispensa a aplicação prévia de técnicas de imputação de dados.

No que diz respeito à tipologia e formato da informação, todas as 31 colunas são de natureza estritamente numérica, não existindo qualquer variável do tipo texto ou categoria em formato _string_ (_object_). Mais especificamente, as 28 variáveis anonimizadas e resultantes da transformação de dimensionalidade por Análise de Componentes Principais (PCA), designadas sequencialmente de V1 a V28, estão formatadas como números de ponto flutuante (_float64_). 

O mesmo formato (_float64_) é partilhado pelas duas únicas variáveis preditivas que mantêm o seu estado original e não sofreram transformação PCA: a variável _Time_, que contabiliza os segundos decorridos entre cada registo e a primeira transação do _dataset_, e a variável _Amount_, que representa o valor monetário exato da operação. Por fim, a variável alvo do nosso estudo, denominada _Class_, encontra-se codificada como um número inteiro binário (int64), assumindo estritamente o valor 0 para identificar transações normais e o valor 1 para assinalar as transações fraudulentas.
  
## 7. Cronograma Interno
| Fase | Data Limite | Entregável Esperado |
| :--- | :--- | :--- |
| M1: Iniciação | 24/02/2026 | Repositório estruturado e Plano de Projeto. |
| M2: Exploração | 24/03/2026 | Notebook de EDA e Dados Processados. |
| M3: Modelação | [Data] | Comparação de algoritmos e métricas. |
| M4: Finalização| [Data] | Pitch e Relatório Final. |
---
*Data de última atualização: [20/03/2026]*
