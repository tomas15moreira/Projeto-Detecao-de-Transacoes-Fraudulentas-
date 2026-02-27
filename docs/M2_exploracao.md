# Milestone 2: Análise Exploratória e Engenharia de Atributos
> **Nota de Revisão:** Este documento pressupõe que o dataset já foi identificado e descrito no
ficheiro `docs/M1_iniciacao.md`. Caso precise de consultar o significado original das variáveis,
deve consultar essa Milestone.
## 1. Análise Exploratória de Dados (EDA)
### 1.1. Distribuição da Variável Alvo
A variável alvo que pretendemos prever neste projeto é a `Class`. Esta variável é de natureza categórica binária, indicando se uma determinada transação de cartão de crédito é legítima (representada pelo valor `0`) ou fraudulenta (representada pelo valor `1`).

Respondendo diretamente à questão da normalidade: por se tratar de um problema de Classificação (variável discreta) e não de Regressão (variável contínua), o conceito de "distribuição normal" ou curva de Gauss não se aplica a este alvo. Em vez disso, a nossa análise foca-se na proporção e no equilíbrio das classes.

Através da análise de frequências, observou-se que a variável apresenta um desequilíbrio extremo. Este cenário é perfeitamente coerente com o contexto de negócio real do setor financeiro, onde o volume esmagador de transações diárias é legítimo, sendo as fraudes eventos anómalos e raros.


**Factos importantes:** A nossa variável alvo `Class` está fortemente desequilibrada. Num universo total de 284.807 transações, temos 99,828% de transações normais (284.315 registos) e apenas 0,172% de transações fraudulentas (492 registos).

**Métricas de Avaliação:** Este desequilíbrio extremo dita que a métrica padrão de Accuracy (Exatidão) será enganadora (Paradoxo da Exatidão). Um modelo "ingénuo" que classifique sempre todas as transações como `0` (legítimas) obterá uma accuracy de 99,83%, mas falhará redondamente o objetivo do negócio, pois deixará passar 100% das fraudes. Por conseguinte, a avaliação do modelo terá de ser ancorada em métricas robustas ao desequilíbrio, nomeadamente: Recall (taxa de deteção de fraudes), Precision (taxa de acerto quando diz que é fraude), F1-Score e a área sob a curva AUPRC (Area Under the Precision-Recall Curve).

### 1.2. Correlações Relevantes
*Quais as variáveis que têm maior relação com o problema? Incluam referências a gráficos que
geraram no Kaggle.*
* **Atributo A vs. Alvo:** (Ex: "Notámos que quanto maior a idade, menor a probabilidade de
cancelamento.")
* **Atributo B vs. Alvo:** (Ex: "O tipo de contrato mensal está fortemente ligado à saída de
clientes.")

## 2. Qualidade dos Dados e Limpeza
### 2.1. Tratamento de Dados em Falta (Missing Data)
* **Colunas afetadas:** [Lista de colunas]
* **Estratégia adotada:** (Ex: "Substituímos os nulos da coluna 'Salário' pela mediana para
evitar o impacto de outliers.")
### 2.2. Outliers e Inconsistências
*Descrevam se encontraram valores impossíveis (ex: idade = 200) e como os resolveram.*
## 3. Engenharia de Atributos (Feature Engineering)
### 3.1. Transformações Realizadas
* **Encoding:** (Ex: "Convertemos a variável 'Género' em numérica usando One-Hot Encoding.")
* **Escalonamento:** (Ex: "Aplicámos o StandardScaler nas variáveis numéricas para que todas
fiquem na mesma escala.")
### 3.2. Criação de Novos Atributos
*Descrevam as variáveis que criaram para ajudar o modelo.*
* **Nova Variável [Nome]:** (Ex: "Criámos a 'Tenure_Per_Year' que divide o tempo de contrato
pela idade do cliente.")
## 4. Dicionário de Dados Final (Pós-Processamento)
*Listagem final das variáveis que serão entregues ao modelo na Fase 3.*
| Atributo | Tipo | Descrição |
| :--- | :--- | :--- |
| `cliente_id` | ID | Removido (não preditivo) |
| `idade_norm` | Float | Idade após normalização |
| `is_premium` | Binary | 1 para clientes com plano superior |
## 5. Conclusões da Fase de Exploração
*O que aprenderam sobre o dataset que não sabiam no final do Milestone 1? Os dados são suficientes
para avançar para a modelação?*
---
*Data de última atualização: [DD/MM/AAAA]* 
