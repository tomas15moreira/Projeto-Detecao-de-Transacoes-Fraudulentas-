# Milestone 3: Modelação e Avaliação
## 1. Estratégia de Modelação
A estratégia definida para esta fase foca-se na construção de um ecossistema de avaliação robusto, capaz de lidar com o extremo desequilíbrio de classes do dataset (onde as fraudes representam apenas 0,17% do total) e de garantir que as métricas refletem o real impacto no negócio.

### 1.1. Arquitetura de Testes e Prevenção de Data Leakage

A preparação dos dados para os algoritmos seguiu as melhores práticas da indústria para garantir o isolamento total dos dados de avaliação:

* *Divisão do Dataset (Train-Test Split):* O conjunto de dados foi dividido numa proporção de *80% para treino* e *20% para teste*.
* *Estratificação (*Stratified Split):** Utilizámos o parâmetro stratify=y para garantir que a raríssima proporção de fraudes é matematicamente preservada em ambos os conjuntos, permitindo que o modelo seja treinado e avaliado num cenário fiel à realidade. Definimos uma semente fixa (random_state=42) para assegurar a reprodutibilidade.
* *Prevenção de Fuga de Dados (*Zero Data Leakage):* O conjunto de teste foi isolado *antes de qualquer transformação final. O escalonamento das variáveis Amount e Hora através do RobustScaler foi aplicado utilizando fit_transform estritamente nos dados de treino, enquanto nos dados de teste foi aplicado apenas o transform. Isto garante que o modelo não aprende inadvertidamente padrões dos dados que usará para ser avaliado.

### 1.2. Definição das Métricas de Desempenho

O objetivo principal do negócio é maximizar a deteção de fraudes (minimizar Falsos Negativos), pois uma fraude não detetada representa o "Risco Máximo" e perda financeira direta para a instituição. Por conseguinte, a Accuracy (Exatidão) foi descartada e a nossa função de avaliação (avaliar_modelo) foi desenhada para extrair os seguintes indicadores:

* *Matriz de Confusão:* Ferramenta visual prioritária para quantificar o número absoluto de transações legítimas bloqueadas (Falsos Positivos) e de fraudes que escaparam (Falsos Negativos).
* *Recall (Sensibilidade):* Métrica de negócio fundamental que indica a percentagem de fraudes reais que o modelo conseguiu capturar. O nosso objetivo base é superar os 85%.
* *AUPRC (Area Under the Precision-Recall Curve):* Selecionada como a *métrica principal do projeto. Ao contrário da curva ROC, a AUPRC é a métrica mais fiável para conjuntos de dados severamente desequilibrados, avaliando o trade-off entre Precisão e Recall. O nosso objetivo SMART estabelecido é alcançar um valor *≥ 0.80**.
* *Métricas Complementares:* F1-Score (média harmónica entre precisão e sensibilidade) e AUC-ROC (calculada por referência, embora analisada com precaução devido ao desequilíbrio).

Para validar toda esta arquitetura de testes antes de avançar para algoritmos complexos ou técnicas de oversampling (SMOTE), treinámos com sucesso um modelo de referência (Baseline) utilizando Regressão Logística.

## 2. Experiências Realizadas
### 2.1. Modelo Baseline
*O ponto de partida simples.*
* **Algoritmo:** (p/ex.: Regressão Logística)
* **Resultado:** (p/ex.: Accuracy: 0.72)
### 2.2. Modelos Candidatos
*Listagem dos algoritmos testados e a justificação da escolha.*
| Algoritmo | Parâmetros Base | Métrica (Treino) | Métrica (Teste) | Notas |
| :--- | :--- | :--- | :--- | :--- |
| Random Forest | n_estimators=100 | 0.95 | 0.82 | Sinais de overfitting |
| XGBoost | default | 0.88 | 0.85 | Melhor generalização |
| SVM | kernel='rbf' | 0.80 | 0.79 | Lento no treino |
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
