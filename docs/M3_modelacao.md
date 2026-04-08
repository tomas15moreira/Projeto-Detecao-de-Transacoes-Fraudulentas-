# Milestone 3: Modelação e Avaliação
## 1. Estratégia de Modelação
A estratégia definida para esta fase foca-se na construção de um ecossistema de avaliação robusto, capaz de lidar com o extremo desequilíbrio de classes do dataset (onde as fraudes representam apenas 0,17% do total) e de garantir que as métricas refletem o real impacto no negócio.

**Divisão do dataset:** Utilizámos uma divisão de 80% para treino e 20% para teste. Esta divisão foi executada de forma estratificada (stratify=y), assegurando que a raríssima proporção de 0,17% de fraudes é preservada tanto no treino como no teste. Definimos uma semente aleatória fixa (random_state=42) para garantir a reprodutibilidade total das experiências e resultados.

**Métrica de Sucesso:** A métrica principal de sucesso definida é a AUPRC (_Area Under the Precision-Recall Curve_), com o objetivo de atingir um valor mínimo de 0,80. Dada a natureza extremamente desequilibrada dos dados, a exatidão (_accuracy_) é uma métrica enganadora; o AUPRC permite avaliar o equilíbrio entre a precisão e a capacidade de deteção em diferentes limiares. Complementarmente, definimos como meta um Recall (Sensibilidade) superior a 85%, garantindo que o modelo captura a vasta maioria das transações fraudulentas reais.

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
