# Milestone 1: Iniciação e Definição do Projeto

## 1. Descrição Detalhada do Problema e Contextualização
O setor bancário enfrenta um desafio contínuo com a fraude em cartões de crédito, que resulta em prejuízos financeiros diretos para as instituições e perda de confiança por parte dos consumidores. O problema central da modelação preditiva neste domínio não é apenas detetar a fraude, mas fazê-lo num cenário de extremo desequilíbrio de classes.

**O Desafio dos Dados e do Negócio**
O _dataset_ selecionado para este projeto, intitulado "Credit Card Fraud Detection" e disponibilizado no Kaggle, ilustra perfeitamente esta realidade. Este conjunto contém transações efetuadas por cartões de crédito europeus em setembro de 2013, onde as fraudes representam uma minoria ínfima: apenas 492 transações fraudulentas num universo de 284.807 transações totais (cerca de 0,172%).

Perante esta distribuição, um modelo ingénuo que previsse matematicamente todas as transações como "legítimas" atingiria uma exatidão (_accuracy_) superior a 99,8%. Contudo, este modelo seria inútil do ponto de vista de negócio, pois falharia a deteção de todas as fraudes reais. 

**O Trade-off de Gestão**
A formulação da solução exige a gestão rigorosa do equilíbrio (_trade-off_) entre dois tipos de erro de classificação, com impactos drásticos na operação bancária:
* **Falsos Negativos (Risco Máximo):** Ocorre quando o modelo deixa passar uma fraude real. O impacto é uma perda financeira direta e irreversível para a instituição.
* **Falsos Positivos (Atrito Operacional):** Ocorre quando uma transação legítima é erradamente sinalizada como fraude. O impacto reflete-se no bloqueio indevido do cartão do cliente, gerando insatisfação severa, perda de confiança e potenciais danos reputacionais.

**Pergunta de Investigação**
Considerando as restrições do _dataset_ (onde as variáveis preditivas originais foram transformadas e anonimizadas por componentes principais - PCA - por motivos de privacidade), a questão central que orienta este projeto é:
* **"Como desenvolver um modelo de Machine Learning capaz de identificar eficazmente transações fraudulentas num cenário de extremo desequilíbrio de classes, minimizando o risco financeiro (Falsos Negativos) sem comprometer gravemente a experiência do cliente (Falsos Positivos)?"**

Para responder a este problema, a solução exigirá a aplicação de técnicas de balanceamento de dados e a seleção de métricas de avaliação que penalizem a incapacidade de deteção da classe minoritária, como a AUPRC (_Area Under the Precision-Recall Curve_).

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
A viabilidade deste projeto assenta na adequação, origem e integridade do conjunto de dados escolhido. Para garantir o rigor científico e a reprodutibilidade do estudo, a análise de viabilidade foi estruturada em duas dimensões principais: origem da informação e critérios de qualidade.

**Disponibilidade e Origem**
O _dataset_ utilizado, intitulado "Credit Card Fraud Detection", é de domínio público e encontra-se disponível na plataforma Kaggle. Os dados originais foram recolhidos e disponibilizados através de uma parceria de investigação oficial entre a empresa Worldline e o _Machine Learning Group_ (MLG) da _Université Libre de Bruxelles_ (ULB). Esta proveniência académica e corporativa atesta a legitimidade e a relevância real da amostra.

**Critérios de Qualidade dos Dados**
A viabilidade técnica do _dataset_ foi validada através da verificação rigorosa dos seguintes critérios de qualidade da informação:
* **Completude (Ausência de Nulos):** O ficheiro não contém qualquer valor ausente (_missing values_, _Nulls_ ou _NAs_) nas suas 284.807 observações. Este facto é crucial, pois elimina a necessidade de aplicar métodos de imputação artificial na fase de pré-processamento, evitando a introdução de viés (_bias_) nos dados.
* **Consistência de Formatação:** Todos os 31 atributos apresentam uma consistência estrutural perfeita, mantendo um formato estritamente numérico (_float64_ para as variáveis independentes e _int64_ para a variável alvo). Não existem erros tipográficos, anomalias de formatação ou dados corrompidos.
* **Representatividade:** O conjunto de dados reflete transações reais capturadas ao longo de 48 horas consecutivas, fornecendo uma janela temporal e comportamental autêntica do mercado europeu.

**Conclusão da Viabilidade**
Conclui-se que o _dataset_ cumpre todos os requisitos técnicos para aplicação de algoritmos de _Machine Learning_. A sua elevada qualidade estrutural garante que o esforço da equipa não será consumido na limpeza básica de ficheiros corrompidos, mas sim focado na resolução dos verdadeiros desafios matemáticos do projeto: o tratamento do extremo desequilíbrio de classes e a modelação preditiva sobre variáveis anonimizadas (PCA).

## 5. Dicionário de Dados

Abaixo apresenta-se o dicionário de dados detalhado, incluindo a descrição, a tipologia e o intervalo de valores (mínimo e máximo) observados no _dataset_ original para cada uma das 31 variáveis:

| Variável | Descrição | Tipo de Dado | Intervalo de Valores (Min - Máx) |
| :--- | :--- | :--- | :--- |
| **Time** | Segundos decorridos entre cada transação e a primeira transação registada no _dataset_. | _Float64_ | [0.0, 172792.0] (Equivalente a 48 horas) |
| **V1 a V28** | 28 variáveis numéricas anonimizadas, resultantes da transformação de dimensionalidade por _Principal Component Analysis_ (PCA) para proteger as identidades dos utilizadores. | _Float64_ | Contínuos. Teoricamente ilimitados, mas empiricamente variam entre [-113.55, 120.58] dependendo da componente. |
| **Amount** | O valor monetário da transação. | _Float64_ | [0.00, 25691.16] |
| **Class** | Variável alvo (_Target_). Indica a legitimidade da transação. | _Int64_ (Categórica) | 0 (Transação Normal) ou 1 (Fraude) |

**Nota sobre a variável _Amount_:** O facto de o valor mínimo ser 0.00 indica a presença de transações de validação de cartão ou autorizações pendentes que não movimentaram fundos reais, algo que será tratado na fase de Análise Exploratória (Milestone 2).

## 6. Descrição Técnica 
Após a importação e verificação inicial do ficheiro _creditcardfraud.csv_, procedeu-se à análise da estrutura técnica do conjunto de dados. O _dataset_ apresenta uma dimensão considerável, sendo composto por exatamente 284.807 registos de transações distribuídos por 31 colunas distintas. Em termos de integridade, a base de dados destaca-se por estar perfeitamente preenchida, não apresentando qualquer valor nulo (NaN) ou ausente em toda a sua extensão, o que atesta a sua fiabilidade e dispensa a aplicação prévia de técnicas de imputação de dados.

No que diz respeito à tipologia e formato da informação, todas as 31 colunas são de natureza estritamente numérica, não existindo qualquer variável do tipo texto ou categoria em formato _string_ (_object_). Mais especificamente, as 28 variáveis anonimizadas e resultantes da transformação de dimensionalidade por Análise de Componentes Principais (PCA), designadas sequencialmente de V1 a V28, estão formatadas como números de ponto flutuante (_float64_). 

O mesmo formato (_float64_) é partilhado pelas duas únicas variáveis preditivas que mantêm o seu estado original e não sofreram transformação PCA: a variável _Time_, que contabiliza os segundos decorridos entre cada registo e a primeira transação do _dataset_, e a variável _Amount_, que representa o valor monetário exato da operação. Por fim, a variável alvo do nosso estudo, denominada _Class_, encontra-se codificada como um número inteiro binário (int64), assumindo estritamente o valor 0 para identificar transações normais e o valor 1 para assinalar as transações fraudulentas.
  
## 7. Resumo e Conclusões da Fase de Iniciação
Nesta fase inicial do projeto (Milestone 1), estabelecemos as fundações teóricas, técnicas e de negócio que guiarão o desenvolvimento do modelo preditivo. Da análise preliminar, destacam-se os seguintes pontos críticos:

* **O Desafio do Desequilíbrio como Foco Central:** Constatou-se que o verdadeiro desafio deste projeto não é a complexidade estrutural dos dados, mas sim a extrema raridade do evento alvo (apenas 0,172% das transações são fraudes). Esta característica ditará obrigatoriamente a necessidade de aplicar técnicas de amostragem avançadas (como o SMOTE) nas fases seguintes.
* **Métricas Alinhadas com o Negócio:** Ficou estabelecido que métricas tradicionais, como a Exatidão (_Accuracy_), são ilusórias neste contexto. O sucesso do projeto será medido pela capacidade de maximizar a deteção de fraudes reais (_Recall_) mantendo a precisão, refletida na nossa métrica principal: a **AUPRC (objetivo ≥ 0.80)**.
* **Viabilidade Técnica Confirmada:** A auditoria preliminar ao _dataset_ confirmou a sua integridade (ausência de valores nulos e formatação 100% numérica). Isto garante que o esforço da equipa na Fase 2 será canalizado para a Análise Exploratória (EDA) e criação de inteligência analítica, sem desperdício de tempo em limpezas básicas.
* **Restrição de Interpretabilidade (PCA):** O facto de 28 das 30 variáveis preditivas estarem ofuscadas por componentes principais (PCA) limitará a capacidade de extrair regras de negócio puramente intuitivas. A resposta a este desafio passará por uma forte aposta em algoritmos de _Machine Learning_ capazes de mapear relações não-lineares ocultas.

Em suma, a equipa encontra-se munida de um conjunto de dados altamente viável e de um plano de ação claro, estando preparada para avançar para a fase de Análise Exploratória de Dados (Milestone 2).

## 8. Cronograma Interno
| Fase | Data Limite | Entregável Esperado |
| :--- | :--- | :--- |
| M1: Iniciação | [24/02/2026] | Repositório estruturado e Plano de Projeto. |
| M2: Exploração | [24/03/2026] | Notebook de EDA e Dados Processados. |
| M3: Modelação | [23/04/2026] | Comparação de algoritmos e métricas. |
| M4: Finalização| [Data] | Pitch e Relatório Final. |
---
*Data de última atualização: [15/04/2026]*
