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

## 2. Experiências Realizadas e Diagnóstico de Desempenho
Nesta fase, o foco transitou da preparação para a experimentação algorítmica, com o objetivo de identificar a arquitetura mais robusta para detetar fraudes.

### 2.1. Modelo Baseline
Para estabelecer um patamar mínimo de desempenho, utilizámos a Regressão Logística. Este modelo serviu para validar a nossa arquitetura de testes antes de avançarmos para algoritmos de maior complexidade.

**Registo de Desempenho (Baseline):**
* **_Recall_ (Sensibilidade):** 0.5789 (O modelo deixou escapar 40 fraudes reais).
* **AUPRC:** 0.6955 (Abaixo da meta de 0.80).
* **Diagnóstico:** Sinais claros de Underfitting (Subajuste). A simplicidade do modelo linear não foi capaz de capturar as relações não-lineares dos dados.

### 2.2. Modelos Candidatos e Análise de Ajuste (Fit)
Avançámos para métodos de _Ensemble Learning_ (Random Forest e XGBoost), reconhecidos pela eficácia em dados desbalanceados.

#### Tabela Comparativa de Desempenho (Treino vs. Teste)
Esta comparação é fundamental para identificar fenómenos de Overfitting (memorização) ou Generalização (aprendizagem real).

| Modelo | Recall (Treino) | Recall (Teste) | F1-Score (Teste) | AUPRC (Teste) |
| :--- | :--- | :--- | :--- | :--- |
| **Baseline (Log. Reg.)** | 61.64% | 57.89% | 68.75% | 69.55% |
| **Random Forest** | 97.35% | 74.74% | 81.14% | 78.05% |
| **XGBoost** | 100% | 78.95% | 85.71% | 82.51% |

#### Análise Crítica e Diagnóstico
* **O Problema do Random Forest (_Overfitting_):** Apesar de um desempenho de treino excelente, o modelo sofreu uma quebra significativa no teste. Isto indica que o algoritmo "decorou" casos específicos do histórico em vez de aprender o padrão geral da fraude.
* **A Vitória do XGBoost (Generalização):** O XGBoost revelou-se o modelo mais promissor. Embora apresente 100% no treino (devido à complexidade inicial), manteve a melhor performance de teste e a maior proximidade ao nosso objetivo SMART (AUPRC > 0.80).
* **Curvas de Aprendizagem:** A análise das curvas confirmou que o XGBoost é estável, mas beneficiaria de uma redução na complexidade dos hiperparâmetros para fechar o "gap" entre treino e teste.

## 3. Otimização (Tuning)
Nesta fase, submetemos o modelo vencedor (XGBoost) a um processo de sintonização fina (_Fine-Tuning_) para explorar se um ajuste exaustivo de hiperparâmetros resultaria em ganhos significativos para os objetivos SMART definidos, comparando a versão base com a versão otimizada.

### 3.1. Técnica e Configuração Ideal Encontrada
Utilizámos o algoritmo _RandomizedSearchCV_ (30 combinações testadas via validação cruzada), focando a otimização na métrica de _Recall_ para garantir a proteção contra o risco financeiro. O algoritmo identificou a seguinte configuração ideal:
* **Parâmetros Ótimos:** max_depth: 3, n_estimators: 200, learning_rate: 0.1, subsample: 0.7, colsample_bytree: 0.8 e scale_pos_weight: 899.21.

### 3.2. Comparação Crítica e Decisão Final (Modelo Base vs. Otimizado)
A análise comparativa entre o desempenho do XGBoost Simples (configuração original) e o XGBoost Tuned (pós-otimização) revelou um _trade-off_ crítico que fundamentou a nossa decisão estratégica:

| Métrica (Teste) | XGBoost Simples (Vencedor) | XGBoost Tuned (Otimizado) | Variação (Delta) |
| :--- | :--- | :--- | :--- |
| **AUPRC** | **0.8251** | 0.7936 | **- 0.0315** |
| **Recall (Sensibilidade)** | 78.95% | **83.16%** | **+ 4.21%** |
| **Precisão** | **93.75%** | 44.89% | **- 48.86%** |
| **F1-Score** | **0.8571** | 0.5830 | **- 0.2741** |

**Análise Crítica e Justificação:**
Embora o processo de _tuning_ tenha conseguido elevar o _Recall_ para 83,16% no teste (e uma média de 85,98% em validação cruzada), fê-lo com um custo operacional desproporcional. A Precisão caiu drasticamente de 93,75% para 44,89% e o F1-Score desceu de 0.86 para 0.58. 

Na prática, para detetar mais 4% de fraudes, o modelo passaria a bloquear indevidamente mais de metade das transações que sinaliza como suspeitas. Esta degradação geraria um atrito inaceitável com clientes legítimos. Além disso, o modelo otimizado baixou a AUPRC para 0.79, falhando o cumprimento do Objetivo SMART 1 (≥ 0.80) no conjunto de teste. 

**Conclusão:** Optámos por manter o XGBoost Simples como a solução final. Esta configuração provou ser a mais equilibrada e robusta, garantindo uma performance superior global e uma proteção financeira elevada sem comprometer a experiência do utilizador.

## 4. Avaliação do Modelo Final
Nesta secção, procedemos à auditoria detalhada do comportamento do modelo XGBoost Simples, identificando não apenas o seu sucesso, mas também a natureza estatística das suas falhas.

### 4.1. Matriz de Confusão e Interpretação de Erros
A análise da matriz de confusão no conjunto de teste revela um desempenho de alta precisão, mas com margem de otimização na captura total de eventos:

* **Verdadeiros Positivos (75):** O modelo identificou com sucesso a grande maioria das fraudes.
* **Falsos Positivos (5):** Apenas 5 clientes legítimos teriam os seus cartões bloqueados indevidamente. Esta "limpeza" operacional é o ponto forte do modelo escolhido.
* **Falsos Negativos (20):** O modelo deixou passar 20 fraudes (Risco Máximo).

**Análise Crítica das Falhas:**
A menor fiabilidade do modelo em detetar estes 20 casos não é um erro algorítmico aleatório, mas sim uma limitação estatística dos dados:
1.  **Escassez de Exemplos (Amostragem):** Mesmo com o ajuste de pesos, o treino conta com apenas ~400 exemplos de fraude contra centenas de milhares de transações normais. Estatisticamente, o modelo tem dificuldade em mapear "subgrupos de fraude" que ocorrem com padrões muito específicos ou raros.
2.  **Sobreposição de Classes (Zonas Cinzentas):** A análise visual sugere que estes Falsos Negativos residem numa zona de intersecção estatística, onde os atributos da transação fraudulenta (valor, tempo e componentes PCA) são matematicamente indistinguíveis de uma transação legítima habitual do utilizador.

### 4.2. Importância dos Atributos (Feature Importance)
A interpretação estatística da decisão do modelo indica que o XGBoost baseia a sua classificação num grupo restrito de variáveis críticas:

1.  **V17 e V14:** Foram identificadas como as variáveis com maior poder discriminatório. Estatisticamente, estas componentes capturam desvios comportamentais extremos que são os maiores indicadores de anomalia.
2.  **V12 e V10:** Atuam como variáveis de suporte, ajudando o modelo a refinar a fronteira de decisão.
3.  **Variáveis de Baixo Impacto:** Notou-se que variáveis como _Time_ têm pouca relevância na decisão final, sugerindo que a fraude neste _dataset*_ não segue um padrão horário rígido, mas sim padrões de valor e tipo de transação (refletidos nas componentes PCA).

### 4.3. Fiabilidade em Cenários Reais
O modelo apresenta elevada fiabilidade para detetar padrões de fraude "óbvios" ou de alto impacto, onde o desvio estatístico é claro. No entanto, a fiabilidade é moderada em ataques de "baixa intensidade" (transações que mimetizam perfeitamente o perfil de consumo do cliente), onde a ausência de variáveis contextuais extra-transacionais (como localização GPS ou ID do dispositivo) impede uma separação perfeita das classes.

## 5. Conclusão da Fase de Modelação
*Justifiquem por que razão este modelo está pronto (ou não) para ser apresentado como solução
final.*
---
*Data de última atualização: [17/04/2026]*
