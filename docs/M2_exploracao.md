# Milestone 2: Análise Exploratória e Engenharia de Atributos
## 1. Análise Exploratória de Dados (EDA)
### 1.1. Distribuição da Variável Alvo
A variável alvo que pretendemos prever neste projeto é a `Class`. Esta variável é de natureza categórica binária, indicando se uma determinada transação de cartão de crédito é legítima (representada pelo valor `0`) ou fraudulenta (representada pelo valor `1`).

Respondendo diretamente à questão da normalidade: por se tratar de um problema de Classificação (variável discreta) e não de Regressão (variável contínua), o conceito de "distribuição normal" ou curva de Gauss não se aplica a este alvo. Em vez disso, a nossa análise foca-se na proporção e no equilíbrio das classes.

Através da análise de frequências, observou-se que a variável apresenta um desequilíbrio extremo. Este cenário é perfeitamente coerente com o contexto de negócio real do setor financeiro, onde o volume esmagador de transações diárias é legítimo, sendo as fraudes eventos anómalos e raros.


**Factos importantes:** A nossa variável alvo `Class` está fortemente desequilibrada. Num universo total de 284.807 transações, temos 99,828% de transações normais (284.315 registos) e apenas 0,172% de transações fraudulentas (492 registos).

**Métricas de Avaliação:** Este desequilíbrio extremo dita que a métrica padrão de Accuracy (Exatidão) será enganadora (Paradoxo da Exatidão). Um modelo "ingénuo" que classifique sempre todas as transações como `0` (legítimas) obterá uma accuracy de 99,83%, mas falhará redondamente o objetivo do negócio, pois deixará passar 100% das fraudes. Por conseguinte, a avaliação do modelo terá de ser ancorada em métricas robustas ao desequilíbrio, nomeadamente: Recall (taxa de deteção de fraudes), Precision (taxa de acerto quando diz que é fraude), F1-Score e a área sob a curva AUPRC (Area Under the Precision-Recall Curve).

### 1.2. Correlações Relevantes
Para identificar as relações mais fortes com o nosso problema, gerámos uma Matriz de Correlação (Heatmap) e uma Matriz de Dispersão (Pairplot) no Kaggle. É importante notar que, como os atributos `V1` a `V28` resultam de uma transformação PCA prévia (para anonimização dos dados), eles não apresentam correlação entre si (correlação zero), o que nos permitiu focar exclusivamente na relação de cada atributo isolado com a variável alvo.



* **Atributos `V17`, `V14` e `V12` vs. Alvo:** Notámos que estas três variáveis apresentam as correlações negativas mais fortes com a probabilidade de fraude (com valores de -0.32, -0.30 e -0.26, respetivamente). Através dos gráficos de dispersão, é visível que quanto menores (e mais negativos) forem os valores nestes atributos, maior é a probabilidade de a transação ser fraudulenta. Os pontos vermelhos (fraudes) isolam-se claramente na extremidade inferior dos eixos destas variáveis.

* **Atributo `V11` vs. Alvo:** Em contraste, a variável `V11` apresenta a correlação positiva mais elevada (0.15) com a nossa variável alvo. Os gráficos demonstram que valores mais altos em `V11` estão frequentemente associados a transações ilícitas.

* **`Hora` vs. Alvo:** Cruzando a nova variável temporal que criámos com os atributos PCA na nossa matriz de dispersão, notámos uma alteração de comportamento dependente da hora. Durante o período da madrugada (ex: 02h00 - 06h00), o volume de transações normais cai drasticamente, mas as transações fraudulentas mantêm um ritmo constante ou apresentam picos relativos. Isto indica que o horário noturno está fortemente ligado a um risco proporcionalmente mais elevado de fraude.


## 2. Qualidade dos Dados e Limpeza
### 2.1. Tratamento de Dados em Falta e Duplicados
O primeiro passo na garantia da qualidade dos dados consistiu em verificar a integridade estrutural do dataset.
* **Valores Nulos (Missing Data):** Através da função `isnull().sum()`, confirmámos que o dataset está 100% preenchido. Não existem valores nulos (`NaN`) em nenhuma das 31 colunas, dispensando o uso de técnicas de imputação.
* **Registos Duplicados:** Aplicámos a verificação de duplicados e identificámos a existência de 1.081 transações repetidas. Estes registos foram removidos do dataset (através do método `drop_duplicates()`), reduzindo o número total de observações de 284.807 para 283.726. Esta ação de limpeza é crucial para evitar o enviesamento (overfitting) do modelo, garantindo que o algoritmo não "decora" transações repetidas.

### 2.2. Outliers e Inconsistências
Num contexto de deteção de fraudes financeiras, a abordagem aos outliers (valores atípicos) exige especial cautela.
* **Inconsistências Lógicas:** Não foram detetados valores impossíveis (como, por exemplo, valores negativos na variável `Amount`). As variáveis resultantes do PCA (`V1` a `V28`) apresentam distribuições coerentes com a sua transformação matemática.
* **Análise de Outliers no Montante (`Amount`):** A análise estatística descritiva e as visualizações geradas no nosso notebook revelaram uma cauda longa à direita na variável `Amount`. Enquanto 75% das transações são inferiores a 78€, o valor máximo atinge os 25.691,16€.
* **Estratégia Adotada:** Decidimos não remover nenhum outlier. Num cenário de fraude, as próprias transações ilícitas são, por definição, anomalias. Remover valores extremos através de métodos tradicionais (como o Intervalo Interquartil - IQR) poderia resultar na eliminação inadvertida das fraudes que pretendemos detetar ou de transações legítimas de clientes . Em vez de remover, a nossa estratégia consistirá em mitigar o peso numérico destes extremos aplicando a técnica de escalonamento `RobustScaler` na fase de modelação.

## 3. Engenharia de Atributos (Feature Engineering)
### 3.1. Transformações Realizadas
* **Encoding**:
A aplicação de técnicas de codificação (*One-Hot Encoding* ou *Label Encoding*) é uma etapa padrão para traduzir variáveis qualitativas (texto) para formatos matemáticos inteligíveis pelos algoritmos de *Machine Learning*. No entanto, após uma avaliação rigorosa do nosso *dataset*, concluímos que a aplicação destas técnicas é **desnecessária** para este projeto, sustentando esta decisão com base nos seguintes fatores técnicos e teóricos:
* **Avaliação Prática do Dataset:** Efetuámos uma análise aos tipos de dados das *features* (variáveis independentes). Constatámos que, devido à transformação PCA prévia (que gerou as componentes `V1` a `V28`) e à natureza das restantes colunas (`Amount` e `Hora_do_Dia`), **100% das variáveis independentes são numéricas contínuas ou discretas**. A única variável categórica presente é a variável alvo (`Class`), que já se encontra nativamente codificada num formato binário numérico (`0` para Legítima, `1` para Fraude).
* **Pré-Requisito Matemático do PCA:** É imperativo compreender a origem dos dados (`V1` a `V28`). A Análise de Componentes Principais (PCA) é um algoritmo de álgebra linear que exige que todos os dados de entrada sejam estritamente numéricos para calcular a matriz de covariância. Isto significa que qualquer codificação necessária nas variáveis originais (ex: "País de Origem" ou "Tipo de Cartão") já foi tratada a montante pelos engenheiros de dados antes da aplicação do PCA. O *dataset* atual já reflete o estado da arte do pré-processamento.
* **Prevenção da "Maldição da Dimensionalidade":** A ausência de necessidade de *One-Hot Encoding* traz vantagens cruciais para a arquitetura do modelo. A codificação de variáveis nominais resulta frequentemente na criação de matrizes esparsas. Num *dataset* com quase 300.000 registos, introduzir dezenas de colunas binárias aumentaria drasticamente o consumo de memória RAM e o tempo de treino dos algoritmos, além de elevar o risco de *overfitting* (sobreajuste). Ao operarmos exclusivamente num espaço numérico denso, mantemos o modelo altamente eficiente.**
  
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
*Data de última atualização: [27/02/2026]* 
