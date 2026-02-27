# Milestone 1: Iniciação e Definição do Projeto

## 1. Descrição Detalhada do Problema
O setor bancário enfrenta um desafio contínuo com a fraude em cartões de crédito, que resulta em prejuízos financeiros diretos para as instituições e perda de confiança por parte dos consumidores. O problema central deste projeto não é apenas detetar a fraude, mas fazê-lo num cenário de **extremo desequilíbrio**: a grande maioria das transações é legítima.

Neste contexto, um modelo que simplesmente classifique todas as transações como "legítimas" teria uma precisão de 99.9%, mas falharia completamente o objetivo de negócio (detetar a fraude). O desafio de gestão reside no *trade-off* entre bloquear transações fraudulentas (evitar Falsos Negativos - perda de dinheiro) e não bloquear transações legítimas (evitar Falsos Positivos - insatisfação do cliente).

O dataset selecionado contém transações efetuadas por cartões de crédito europeus em setembro de 2013, onde as variáveis de entrada foram anonimizadas através de uma transformação PCA (Principal Component Analysis) para proteção de dados sensíveis.

## 2. Objetivos SMART
1.  **Objetivo 1:** Desenvolver um modelo de classificação para identificar transações fraudulentas em cartões de crédito com uma métrica AUPRC mínima de 0.80, utilizando o dataset disponibilizado no Kaggle, para ser validado e apresentado no Milestone 3.
2.  **Objetivo 2:** Aplicar a técnica SMOTE (oversampling) para corrigir o desequilíbrio extremo dos dados, treinando um algoritmo que atinja uma Sensibilidade (Recall) superior a 85%, garantindo a minimização de perdas financeiras (falsos negativos), até à entrega final do projeto.

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
    * **Gestão de Tarefas:** GitHub Projects (Kanban: *To Do, In Progress, Done*).
    * **Comunicação:** Grupo dedicado no WhatsApp/Discord para *dailies* rápidas.
    * **Controlo de Versão:** Git (Branching strategy: `main` para produção, branches individuais para desenvolvimento).

## 4. Análise de Viabilidade dos Dados
* **Disponibilidade:** Os dados foram obtidos via Kaggle (*Credit Card Fraud Detection*) e já se encontram no repositório na pasta `data/raw/`.
* **Qualidade Inicial:** O dataset é considerado de "alta qualidade" técnica (limpo), com 284.807 registos. Não existem valores nulos (NaN), o que acelera a fase de limpeza.
* **Ética:**
    * Os dados cumprem rigorosos padrões de privacidade. As variáveis `V1` a `V28` são o resultado de uma transformação PCA, garantindo que não temos acesso a dados pessoais identificáveis (PII) dos utilizadores, o que elimina barreiras éticas no tratamento da informação.
    * As únicas variáveis não transformadas são `Time` e `Amount`.

## 5. Dicionário de Dados 

O dataset utilizado neste projeto contém transações efetuadas por titulares de cartões de crédito europeus durante o mês de setembro de 2013. A base de dados é composta por 284.807 registos e 31 variáveis, refletindo um cenário real de fraude bancária caracterizado por um desequilíbrio extremo de classes (as fraudes representam apenas 0,172% do total de transações).

Uma particularidade técnica fundamental deste conjunto de dados é o seu elevado nível de anonimização. Para proteger a privacidade dos utilizadores e os dados sensíveis das operações, as características originais das transações foram submetidas a uma Análise de Componentes Principais (PCA). Como resultado, a maioria das variáveis explicativas (`V1` a `V28`) consiste em componentes numéricas contínuas cuja identidade original foi ocultada, restando apenas o tempo e o montante como variáveis ativas diretamente interpretáveis.

| Variável | Tipo de Dados | Descrição Principal |
| :--- | :--- | :--- |
| **`Time`** | Numérico Contínuo | Número de segundos decorridos entre a transação atual e a primeira transação registada no dataset. Útil para análise de padrões temporais. |
| **`V1` a `V28`** | Numérico Contínuo | 28 variáveis resultantes de uma transformação de dimensionalidade PCA. Os dados originais foram ocultados para cumprir normas de privacidade bancária. |
| **`Amount`** | Numérico Contínuo | Valor monetário da transação. Esta variável mantém a sua escala original (não sofreu transformação PCA). |
| **`Class`** | Categórico Binário | Variável alvo. O valor **`1`** indica uma transação fraudulenta e o valor **`0`** indica uma transação legítima. |

## 6. Descrição Técnica e Verificação de Integridade

Após a importação e verificação inicial do conjunto de dados `creditcardfraud.csv`, documentamos as seguintes impressões técnicas:

* **Dimensão do Dataset:** O conjunto de dados é composto por 284.807 registos (transações) e 31 colunas (variáveis).
* **Verificação de Valores Nulos (Missing Values):** Foi efetuada uma verificação de integridade e **não existem valores nulos (NaN)** em nenhuma das colunas. O dataset encontra-se perfeitamente preenchido, dispensando a necessidade de técnicas de imputação de dados.
* **Tipos de Dados (Data Types):** Todas as 31 variáveis são estritamente numéricas, não existindo variáveis do tipo `object` (texto/categorias de strings).
  * As variáveis transformadas por PCA (`V1` a `V28`), bem como as variáveis `Time` e `Amount`, são do tipo ponto flutuante (`float64`).
  * A variável alvo, `Class` (onde 0 = Normal e 1 = Fraude), é do tipo inteiro (`int64`).
* **Conclusão Inicial:** O dataset apresenta uma excelente integridade estrutural. Por não possuir valores ausentes ou variáveis categóricas por tratar, a fase de pré-processamento poderá focar-se inteiramente no escalonamento (*scaling*) das variáveis `Time` e `Amount` e, principalmente, na resolução do extremo desequilíbrio da variável `Class`.
  
## 7. Cronograma Interno
| Fase | Data Limite | Entregável Esperado |
| :--- | :--- | :--- |
| M1: Iniciação | 24/02/2026 | Repositório estruturado e Plano de Projeto. |
| M2: Exploração | [Data] | Notebook de EDA e Dados Processados. |
| M3: Modelação | [Data] | Comparação de algoritmos e métricas. |
| M4: Finalização| [Data] | Pitch e Relatório Final. |
---
*Data de última atualização: [24/02/2026]*
