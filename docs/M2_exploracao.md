# Milestone 2: Análise Exploratória e Engenharia de Atributos
## 1. Análise Exploratória de Dados (EDA)
### 1.1. Distribuição da Variável Alvo
A variável alvo que pretendemos prever neste projeto é a _`Class`_. Esta variável é de natureza categórica binária, indicando se uma determinada transação de cartão de crédito é legítima (representada pelo valor `0`) ou fraudulenta (representada pelo valor `1`).

Respondendo diretamente à questão da normalidade: por se tratar de um problema de Classificação (variável discreta) e não de Regressão (variável contínua), o conceito de "distribuição normal" ou curva de Gauss não se aplica a este alvo. Em vez disso, a nossa análise foca-se na proporção e no equilíbrio das classes.

Através da análise de frequências, observou-se que a variável apresenta um desequilíbrio extremo. Este cenário é perfeitamente coerente com o contexto de negócio real do setor financeiro, onde o volume esmagador de transações diárias é legítimo, sendo as fraudes eventos anómalos e raros.


**Factos importantes:** A nossa variável alvo _`Class`_ está fortemente desequilibrada. Num universo total de 284.807 transações, temos 99,828% de transações normais (284.315 registos) e apenas 0,172% de transações fraudulentas (492 registos).

**Métricas de Avaliação:** Este desequilíbrio extremo dita que a métrica padrão de _Accuracy_ (Exatidão) será enganadora. Um modelo "ingénuo" que classifique sempre todas as transações como `0` obterá uma accuracy de 99,83%, mas falhará redondamente o objetivo do negócio, pois deixará passar 100% das fraudes. Por conseguinte, a avaliação do modelo terá de ser ancorada em métricas robustas ao desequilíbrio, nomeadamente: _Recall_ (taxa de deteção de fraudes), _Precision_ (taxa de acerto quando diz que é fraude), _F1-Score_ e a área sob a curva AUPRC (_Area Under the Precision-Recall Curve_).

### 1.2. Correlações Relevantes
Para identificar as relações mais fortes com o nosso problema, gerámos uma Matriz de Correlação (_Heatmap_) e uma Matriz de Dispersão (_Pairplot_) no Kaggle. É importante notar que, como os atributos `V1` a `V28` resultam de uma transformação PCA prévia (para anonimização dos dados), eles não apresentam correlação entre si (correlação zero), o que nos permitiu focar exclusivamente na relação de cada atributo isolado com a variável alvo.



* **Atributos `V17`, `V14` e `V12` vs. Alvo:** Notámos que estas três variáveis apresentam as correlações negativas mais fortes com a probabilidade de fraude (com valores de -0.32, -0.30 e -0.26, respetivamente). Através dos gráficos de dispersão, é visível que quanto menores (e mais negativos) forem os valores nestes atributos, maior é a probabilidade de a transação ser fraudulenta. Os pontos vermelhos (fraudes) isolam-se claramente na extremidade inferior dos eixos destas variáveis.

* **Atributo `V11` vs. Alvo:** Em contraste, a variável `V11` apresenta a correlação positiva mais elevada (0.15) com a nossa variável alvo. Os gráficos demonstram que valores mais altos em `V11` estão frequentemente associados a transações ilícitas.

* **`Hora` vs. Alvo:** Cruzando a nova variável temporal que criámos com os atributos PCA na nossa matriz de dispersão, notámos uma alteração de comportamento dependente da hora. Durante o período da madrugada (ex: 02h00 - 06h00), o volume de transações normais cai drasticamente, mas as transações fraudulentas mantêm um ritmo constante ou apresentam picos relativos. Isto indica que o horário noturno está fortemente ligado a um risco proporcionalmente mais elevado de fraude.


## 2. Qualidade dos Dados e Limpeza
### 2.1. Tratamento de Dados em Falta e Duplicados
O primeiro passo na garantia da qualidade dos dados consistiu em verificar a integridade estrutural do _dataset_.
* **Valores Nulos (_Missing Data_):** Através da função _`isnull().sum()`_, confirmámos que o _dataset_ está 100% preenchido. Não existem valores nulos (`NaN`) em nenhuma das 31 colunas, dispensando o uso de técnicas de imputação.
* **Registos Duplicados:** Aplicámos a verificação de duplicados e identificámos a existência de 1.081 transações repetidas. Estes registos foram removidos do _dataset_ (através do método _`drop_duplicates()`_), reduzindo o número total de observações de 284.807 para 283.726. Esta ação de limpeza é crucial para evitar o enviesamento (_overfitting_) do modelo, garantindo que o algoritmo não "decora" transações repetidas.

### 2.2. Outliers e Inconsistências
Num contexto de deteção de fraudes financeiras, a abordagem aos _outliers_ (valores atípicos) exige especial cautela.
* **Inconsistências Lógicas:** Não foram detetados valores impossíveis (como, por exemplo, valores negativos na variável _`Amount`_). As variáveis resultantes do PCA (`V1` a `V28`) apresentam distribuições coerentes com a sua transformação matemática.
* **Análise de Outliers no Montante (_`Amount`_):** A análise estatística descritiva e as visualizações geradas no nosso _notebook_ revelaram uma cauda longa à direita na variável _`Amount`_. Enquanto 75% das transações são inferiores a 78€, o valor máximo atinge os 25.691,16€.
* **Estratégia Adotada:** Decidimos não remover nenhum _outlier_. Num cenário de fraude, as próprias transações ilícitas são, por definição, anomalias. Remover valores extremos através de métodos tradicionais (como o Intervalo Interquartil - IQR) poderia resultar na eliminação inadvertida das fraudes que pretendemos detetar ou de transações legítimas de clientes . Em vez de remover, a nossa estratégia consistirá em mitigar o peso numérico destes extremos aplicando a técnica de escalonamento _`RobustScaler`_ na fase de modelação.

## 3. Engenharia de Atributos (Feature Engineering)
### 3.1. Transformações Realizadas
* **Encoding**:
A aplicação de técnicas de codificação (_One-Hot Encoding ou Label Encoding_) é uma etapa padrão para traduzir variáveis qualitativas (texto) para formatos matemáticos inteligíveis pelos algoritmos de _Machine Learning_. No entanto, após uma avaliação rigorosa do nosso _dataset_, concluímos que a aplicação destas técnicas é desnecessária para este projeto, sustentando esta decisão com base nos seguintes fatores técnicos e teóricos:
* **Avaliação Prática do Dataset:** Efetuámos uma análise aos tipos de dados das _features_ (variáveis independentes). Constatámos que, devido à transformação PCA prévia (que gerou as componentes `V1` a `V28`) e à natureza das restantes colunas (`Amount` e `Hora_do_Dia`), 100% das variáveis independentes são numéricas contínuas ou discretas. A única variável categórica presente é a variável alvo (`Class`), que já se encontra nativamente codificada num formato binário numérico (`0` para Legítima, `1` para Fraude).
* **Pré-Requisito Matemático do PCA:** É imperativo compreender a origem dos dados (`V1` a `V28`). A Análise de Componentes Principais (PCA) é um algoritmo de álgebra linear que exige que todos os dados de entrada sejam estritamente numéricos para calcular a matriz de covariância. Isto significa que qualquer codificação necessária nas variáveis originais (ex: "País de Origem" ou "Tipo de Cartão") já foi tratada a montante pelos engenheiros de dados antes da aplicação do PCA. O _dataset_ atual já reflete o estado da arte do pré-processamento.
* **Prevenção da "Maldição da Dimensionalidade":** A ausência de necessidade de _One-Hot Encoding_ traz vantagens cruciais para a arquitetura do modelo. A codificação de variáveis nominais resulta frequentemente na criação de matrizes esparsas. Num _dataset_ com quase 300.000 registos, introduzir dezenas de colunas binárias aumentaria drasticamente o consumo de memória RAM e o tempo de treino dos algoritmos, além de elevar o risco de _overfitting_. Ao operarmos exclusivamente num espaço numérico denso, mantemos o modelo altamente eficiente.
  
* **Escalonamento:** A adequação da escala das variáveis é um pré-requisito crítico para garantir que os algoritmos de _Machine Learning_ (especialmente os baseados em cálculos de distância ou gradiente descendente) não atribuam um peso desproporcional a variáveis apenas por terem valores absolutos maiores. 

No nosso _dataset_, as features V1 a V28 já se encontravam transformadas e escalonadas devido à aplicação prévia de PCA pelo emissor do cartão. O nosso desafio residia exclusivamente na uniformização das variáveis _Amount_ (Montante da transação) e _Time_ (Tempo decorrido).

* **Avaliação de Técnicas Tradicionais (Standardization vs. Normalization):** Analisámos a viabilidade das técnicas clássicas. A _Normalization_ (Min-Max Scaler) revelou-se inadequada: devido à presença de _outliers_ extremos no _Amount_ (com transações a atingir os 25.691€), esta técnica comprimiria a esmagadora maioria das transações legítimas (que rondam os 10€ a 80€) num intervalo infinitesimal próximo de zero. Da mesma forma, a _Standardization_ (Standard Scaler) baseia-se na média e no desvio padrão, métricas que são fortemente distorcidas por estes mesmos valores atípicos.
* **Estratégia Adotada (Robust Scaling):** Uma vez que na fase de "Qualidade de Dados" decidimos manter os _outliers_ extremos (por conterem instâncias confirmadas de fraude financeira), a nossa escolha técnica recaiu sobre o _RobustScaler_ da biblioteca _Scikit-Learn_. 
* **Justificação Matemática:** O _Robust Scaler_ utiliza a Mediana e o Intervalo Interquartil (IQR) para efetuar o redimensionamento. Ao ignorar a média clássica, este método permitiu-nos colocar o _Amount_ e o _Time_ numa escala comparável à das variáveis PCA, garantindo que os valores monetários extremos não distorcessem a distribuição matemática global dos dados de treino.

### 3.2. Criação de Novos Atributos
Para enriquecer a capacidade preditiva do modelo e extrair inteligência de negócio das variáveis originais, criámos as seguintes variáveis através do agrupamento de dados:

* **Nova Variável `Periodo_do_Dia`**: Criada a partir da transformação da variável temporal (hora). Em vez de o modelo analisar 24 horas distintas, agrupámos as transações em grandes blocos comportamentais (ex: Manhã, Tarde, Noite, Madrugada). Isto ajuda o modelo a capturar tendências de risco mais amplas, visto que a nossa análise exploratória demonstrou que a proporção de fraudes aumenta significativamente em períodos de menor atividade legítima (como a madrugada).
* **Nova Variável `Nivel_da_Transacao_Monetaria`**: Criada a partir da variável contínua `Amount`. Esta variável segmenta os valores monetários em diferentes escalões ou níveis (ex: Baixo, Médio, Alto). Esta categorização ajuda o modelo ao reduzir o "ruído" das variações exatas de cêntimos e euros, permitindo que os algoritmos de _Machine Learning_ (como árvores de decisão) identifiquem rapidamente se o perfil da fraude está associado a transações de teste (níveis baixos) ou a extrações de grande valor (níveis altos).

## 4. Dicionário de Dados Final (Pós-Processamento)
A tabela seguinte apresenta a estrutura final do *dataset* processado que será utilizado na fase de modelação (Fase 3), detalhando os atributos finais e os respetivos métodos de pré-processamento aplicados:

| Atributo | Tipo | Descrição | Método Aplicado |
| :--- | :--- | :--- | :--- |
| **`Time`** | Float | Tempo original decorrido (em segundos) desde a primeira transação. |  |
| **`Amount`** | Float | Valor monetário original da transação. |  |
| **`V1` a `V28`** | Float | 28 componentes numéricas mantidas intactas, resultantes da anonimização dos dados bancários. |  |
| **`Class`** | Inteiro | Variável alvo binária (0 = Legítima; 1 = Fraude). |  |
| **`Hora`** | Inteiro | Hora do dia (0 a 23) em que ocorreu a transação. | Transformação Matemática (a partir de `Time`) |
| **`Periodo_do_Dia`** | Categoria (Texto) | Segmentação da hora em 4 blocos temporais (Madrugada, Manhã, Tarde, Noite). | Discretização |
| **`Nivel_da_Transacao_Monetaria`**| Categoria (Texto) | Segmentação do montante em 4 escalões (Baixo, Médio, Alto, Muito Alto). | Discretização |
| **`scaled_hora`** | Float | Versão escalonada da variável temporal para uniformização da escala. | `RobustScaler` |
| **`scaled_amount`** | Float | Versão escalonada da variável de montante, mitigando o peso de _outliers_. | `RobustScaler` |

## 5. Conclusões da Fase de Exploração
*O que aprenderam sobre o dataset que não sabiam no final do Milestone 1? Os dados são suficientes
para avançar para a modelação?*
---
*Data de última atualização: [20/03/2026]* 
