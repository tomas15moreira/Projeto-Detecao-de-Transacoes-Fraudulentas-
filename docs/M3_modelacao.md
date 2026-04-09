# Milestone 3: Modelação e Avaliação
## 1. Estratégia de Modelação
A estratégia definida para esta fase foca-se na construção de um ecossistema de avaliação robusto, capaz de lidar com o extremo desequilíbrio de classes do _dataset_ (onde as fraudes representam apenas 0,17% do total) e de garantir que as métricas refletem o real impacto no negócio.

### 1.1. Arquitetura de Testes e Prevenção de Data Leakage

A preparação dos dados para os algoritmos seguiu as melhores práticas da indústria para garantir o isolamento total dos dados de avaliação:

* **Divisão do _Dataset_ (_Train-Test Split_):** O conjunto de dados foi dividido numa proporção de 80% para treino e 20% para teste.
* **Estratificação (_Stratified Split_):** Utilizámos o parâmetro stratify=y para garantir que a raríssima proporção de fraudes é matematicamente preservada em ambos os conjuntos, permitindo que o modelo seja treinado e avaliado num cenário fiel à realidade. Definimos uma semente fixa (random_state=42) para assegurar a reprodutibilidade.
* **Prevenção de Fuga de Dados (_Zero Data Leakage_):** O conjunto de teste foi isolado antes de qualquer transformação final. O escalonamento das variáveis _Amount_ e Hora através do _RobustScaler_ foi aplicado utilizando _fit_transform_ estritamente nos dados de treino, enquanto nos dados de teste foi aplicado apenas o _transform_. Isto garante que o modelo não aprende inadvertidamente padrões dos dados que usará para ser avaliado.

### 1.2. Definição das Métricas de Desempenho

O objetivo principal do negócio é maximizar a deteção de fraudes (minimizar Falsos Negativos), pois uma fraude não detetada representa o "Risco Máximo" e perda financeira direta para a instituição. Por conseguinte, a _Accuracy_ (Exatidão) foi descartada e a nossa função de avaliação (avaliar_modelo) foi desenhada para extrair os seguintes indicadores:

* **Matriz de Confusão:** Ferramenta visual prioritária para quantificar o número absoluto de transações legítimas bloqueadas (Falsos Positivos) e de fraudes que escaparam (Falsos Negativos).
* **Recall (Sensibilidade):** Métrica de negócio fundamental que indica a percentagem de fraudes reais que o modelo conseguiu capturar. O nosso objetivo base é superar os 85%.
* **AUPRC (_Area Under the Precision-Recall Curve_):** Selecionada como a métrica principal do projeto. Ao contrário da curva ROC, a AUPRC é a métrica mais fiável para conjuntos de dados severamente desequilibrados, avaliando o _trade-off_ entre Precisão e _Recall_. O nosso objetivo SMART estabelecido é alcançar um valor ≥ 0.80.
* **Métricas Complementares:** F1-Score (média harmónica entre precisão e sensibilidade) e AUC-ROC (calculada por referência, embora analisada com precaução devido ao desequilíbrio).

Para validar toda esta arquitetura de testes antes de avançar para algoritmos complexos ou técnicas de _oversampling_ (SMOTE), treinámos com sucesso um modelo de referência (Baseline) utilizando Regressão Logística.

## 2. Experiências Realizadas
### 2.1. Modelo Baseline
Para estabelecer um patamar mínimo de desempenho e validar a nossa arquitetura de testes, iniciámos a fase de experimentação com o treino de um algoritmo de baixa complexidade: a Regressão Logística (_LogisticRegression_). 

Este modelo foi treinado exclusivamente com os dados de treino originais processados, preservando propositadamente o desequilíbrio extremo de classes (sem a aplicação de técnicas de _oversampling_ como o SMOTE), com o objetivo de estabelecer um referencial realista.

**Configuração do Modelo Base:**
* **Algoritmo:** Regressão Logística.
* **Parâmetros:** max_iter=1000 (para garantir a convergência matemática sem erros) e random_state=42 (para garantir a reprodutibilidade dos resultados).

**Registo de Desempenho:**
Após o treino, abrimos o conjunto de teste (isolado) e avaliámos o modelo. Os resultados obtidos definem o nosso ponto de partida obrigatório:

* **Recall (Sensibilidade):** 0.5789.
* **Precision (Precisão):** 0.8462.
* **F1-Score:** 0.6875.
* **AUPRC:** 0.6955
* **Matriz de Confusão:** O modelo deixou escapar 40 fraudes (Falsos Negativos), representando o risco máximo.

**Análise e Conclusão do Baseline:**
Como teorizado na fase de Análise Exploratória, um modelo padrão treinado em dados com extrema disparidade de classes sofre na deteção da classe minoritária. Embora a precisão possa ser relativamente alta, o Recall encontra-se significativamente abaixo do nosso objetivo de negócio (> 85%) e a AUPRC não atinge a meta delineada (≥ 0.80). 

Este modelo define o "chão" analítico do projeto. Todas as futuras otimizações e arquiteturas avançadas de _Machine Learning_ (como a introdução de pesos balanceados nos modelos Random Forest e XGBoost) terão a obrigatoriedade de superar as métricas aqui registadas para provarem a sua viabilidade de negócio.
### 2.2. Modelos Candidatos
Após o estabelecimento do baseline, avançámos para a fase de experimentação com algoritmos de maior complexidade. A escolha recaiu sobre métodos de Ensemble Learning, especificamente arquiteturas de árvores de decisão, por serem reconhecidamente eficazes em lidar com relações não-lineares e dados desbalanceados.

#### Algoritmos Selecionados e Configuração
1. *Random Forest (Bagging):* Escolhido pela sua robustez e capacidade de reduzir a variância. Para lidar com o desequilíbrio, utilizámos o parâmetro class_weight='balanced', que ajusta os pesos das classes inversamente proporcional às suas frequências no treino.
2. *XGBoost (Gradient Boosting):* Selecionado pela sua alta performance e eficiência computacional. Implementámos a técnica de scale_pos_weight, calculando o rácio matemático exato entre as classes para penalizar mais severamente os erros na classe minoritária (fraude).

#### Resultados de Desempenho (Tabela Comparativa)

A tabela abaixo resume a performance obtida nos conjuntos de Treino e Teste. Esta comparação é fundamental para identificar possíveis problemas de overfitting (onde o modelo decora o treino mas falha no teste).

| Modelo | Recall (Treino) | Recall (Teste) | F1-Score (Teste) | AUPRC (Teste) |
| :--- | :--- | :--- | :--- | :--- |
| *Baseline (Log. Reg.)* | [61.64 %] | [57.89 %] | [72.02 %] | [68.75 %] |
| *Random Forest* | [Inserir %] | [Inserir %] | [Inserir %] | [Inserir %] |
| *XGBoost* | [Inserir %] | [Inserir %] | [Inserir %] | [Inserir %] |

#### Análise Crítica da Experimentação

* *O Algoritmo de Destaque:* O *[Escolhe o vencedor, ex: XGBoost]* revelou-se o modelo mais promissor. Não só superou largamente o baseline em termos de *Recall* (conseguindo capturar mais fraudes reais), como manteve uma *AUPRC* consistente e próxima do nosso objetivo de 0.80. A sua capacidade de generalização é superior, apresentando uma diferença mínima entre as métricas de treino e teste.
* *Onde houve dificuldades:* O *[Escolhe o que teve menos performance, ex: Random Forest], embora tenha apresentado resultados de treino quase perfeitos, demonstrou uma ligeira tendência para o *overfitting ou uma precisão inferior comparativamente ao XGBoost. Isto sugere que, apesar de capturar as fraudes, o modelo gera um volume superior de alarmes falsos (Falsos Positivos), o que teria um impacto operacional negativo no negócio.

*Conclusão desta fase:* Com base nesta análise comparativa, o modelo selecionado para a fase de otimização final (Hyperparameter Tuning) será o *[Nome do Modelo Vencedor]*, por apresentar o melhor equilíbrio entre a proteção financeira (deteção de fraude) e a experiência do utilizador (minimização de bloqueios indevidos).

## 3. Otimização (Tuning)
*Descrevam como melhoraram o melhor modelo.*
* **Técnica Utilizada:** (p/ex.: "Utilizámos GridSearchCV para ajustar os hiperparâmetros
`max_depth` e `learning_rate`.")
* **Melhoria obtida:** (p/ex.: "O F1-Score subiu de 0.85 para 0.88 após o ajuste.")
## 4. Avaliação do Modelo Final
### 4.1. Matriz de Confusão / Erros
*Analisem onde o modelo mais falha.*
> **Análise:** (p/ex.: "O modelo ainda confunde a Classe A com a Classe B em 10% dos casos devido
à semelhança nos atributos X e Y.")
### 4.2. Importância dos Atributos (Feature Importance)
*Quais as variáveis que o modelo considerou mais importantes para decidir?*
1. [Variável X]
2. [Variável Y]
## 5. Conclusão da Fase de Modelação
*Justifiquem por que razão este modelo está pronto (ou não) para ser apresentado como solução
final.*
---
*Data de última atualização: [DD/MM/AAAA]*
