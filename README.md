# Deteção de Transações Fraudulentas em Cartões de Crédito: Modelação Preditiva em Contexto de Dados Severamente Desbalanceados

## Identificação da Equipa
* **Grupo nº:** 8
* **Membros:**
  * **João Freire** — a2023128832
  * **Rodrigo Ferrão** — a2022138105
  * **Tomás Moreira** — a2023143375

## Identificação Kaggle
* **Nome do Notebook:** Projeto_Detecção_de_Transações_Fraudulentas
* **Autores:**
  * Tomás Moreira — [tomsalm](https://www.kaggle.com/tomsalm)
  * Rodrigo Ferrão — [rodrigoferrao24](https://www.kaggle.com/rodrigoferrao24)
  * João Freire — [joaodfeire2004](https://www.kaggle.com/joaodfeire2004)
* **Link do Notebook:** [Kaggle — Projeto Deteção de Transações Fraudulentas](https://www.kaggle.com/code/tomsalm/projeto-detec-o-de-transa-es-fraudulentas)
* **Link do Dataset:** [Kaggle — Credit Card Fraud Detection](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud)
* **Ambiente de Computação:** Kaggle Notebooks (Python 3, GPU desativada)

## Organização do Repositório

A estrutura deste projeto segue as boas práticas de Ciência de Dados e Engenharia de Software:

* **`data/`**: Armazenamento de dados (dados brutos em `raw/` e processados em `processed/`).
* **`docs/`**: Documentação técnica detalhada dividida por Milestones (M1, M2 e M3).
* **`notebooks/`**: Jupyter Notebooks para experimentação, limpeza e modelação.
* **`reports/`**: Relatórios finais, apresentações e exportação de figuras (`figures/`).
* **`src/`**: Código-fonte modular (scripts `.py`) para funções reutilizáveis.
* **`requirements.txt`**: Ficheiro de configuração com as bibliotecas necessárias.

---

## 1. Iniciação (Milestone 1)

### Contexto e Problema de Negócio

A fraude com cartões de crédito gera prejuízos financeiros massivos para bancos e consumidores. O desafio central é identificar transações fraudulentas em tempo real. No entanto, este é um problema de "procurar uma agulha no palheiro": a grande maioria das transações é legítima, tornando a deteção difícil sem gerar muitos alarmes falsos.

**O Desafio Técnico:** O *dataset* apresenta um desequilíbrio extremo (apenas 0,172% das transações são fraudes), o que invalida métricas tradicionais como a Acurácia e exige abordagens metodológicas específicas para classes minoritárias.

### Resumo da Análise Inicial ao Conjunto de Dados

Antes da definição dos objetivos, foi realizada uma inspeção preliminar ao *dataset* da qual se destacam as seguintes características estruturais:

| Característica | Observação | Implicação para o projeto |
| :--- | :--- | :--- |
| **Dimensão** | 284.807 transações × 31 colunas | Volume adequado para algoritmos de *ensemble*. |
| **Integridade** | Zero valores nulos em todas as colunas | Dispensa estratégias de imputação. |
| **Variável-alvo** | `Class` (0 = Normal, 1 = Fraude) | Problema de classificação binária supervisionada. |
| **Variáveis preditoras** | V1–V28 (anonimizadas por PCA) + `Time` + `Amount` | Sem interpretação de negócio direta nas V1–V28; exige modelos não paramétricos. |
| **Desequilíbrio** | 0,172% de fraudes (492 em 284.807) | Maior desafio técnico — condiciona métricas, algoritmos e estratégia de validação. |
| **Período coberto** | 48 horas consecutivas de transações | Permite criação de *features* temporais (`Hora`, `Periodo_do_Dia`). |

### Objetivos do Projeto (SMART)

* **Objetivo 1 (Preditivo — Qualidade Global):** Desenvolver um modelo de classificação supervisionada para identificar transações fraudulentas em cartões de crédito com uma **AUPRC (*Area Under Precision-Recall Curve*) mínima de 0.80** no conjunto de teste, validado por *Stratified K-Fold Cross-Validation*, até à entrega do *Milestone 3* (23/04/2026).

* **Objetivo 2 (Preditivo — Sensibilidade):** Desenvolver um modelo de classificação supervisionada que atinja uma **Sensibilidade (*Recall*) superior a 85%** na deteção de transações fraudulentas no conjunto de teste, minimizando os Falsos Negativos (fraudes não detetadas, que representam o maior risco financeiro), até à entrega do *Milestone 3* (23/04/2026).

### Perguntas de Investigação

1. Existe uma correlação direta entre o montante da transação e a probabilidade de esta ser classificada como fraude, ou as fraudes tendem a ocorrer em valores mais baixos para passar despercebidas?
2. Quais são as 3 variáveis que mais contribuem para a previsão correta de uma transação ilícita?
3. Existem padrões temporais específicos que sejam mais comuns nas transações fraudulentas comparativamente às transações legítimas?
4. A aplicação de técnicas de reamostragem sintética (*SMOTE — Synthetic Minority Over-sampling Technique*) sobre os dados de treino melhora significativamente a capacidade de deteção de fraudes (*Recall*) face ao tratamento nativo de desequilíbrio via `scale_pos_weight`?

### Fonte de Dados

* **Dataset:** [Credit Card Fraud Detection (Kaggle)](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud)
* **Dimensão:** 284.807 transações, 31 colunas.
* **Origem científica:** dados recolhidos e disponibilizados pelo *Machine Learning Group* da *Université Libre de Bruxelles* (ULB) em parceria com a empresa Worldline, no âmbito de investigação sobre deteção de fraude em transações de cartão de crédito.

---

## 2. Exploração (Milestone 2)

### Limpeza e Preparação

* **Valores Nulos:** Confirmou-se a integridade total do *dataset* (zero valores nulos em todas as colunas).
* **Registos Duplicados:** Foram identificadas e removidas 1.081 transações duplicadas, resultando num conjunto final de 283.726 registos únicos. Esta limpeza é fundamental para evitar o enviesamento (*overfitting*) dos modelos.
* **Tratamento de Outliers:** Optou-se estrategicamente por manter os valores extremos (especialmente na variável `Amount`), dado que num contexto de deteção de fraude, a remoção cega de *outliers* poderia eliminar as próprias transações anómalas que pretendemos prever. A amplitude dos valores foi tratada com **`RobustScaler`** (mais robusto a *outliers* do que o `StandardScaler`) na fase de modelação.

### Engenharia de Atributos (*Feature Engineering*)

A partir das variáveis originais, foram criadas duas variáveis categóricas que codificam conhecimento de negócio:

* **`Periodo_do_Dia`** (Madrugada / Manhã / Tarde / Noite) — derivada da variável `Hora` (que por sua vez foi criada a partir de `Time`).
* **`Nivel_da_Transacao_Monetaria`** (Micro / Baixa / Média / Alta) — derivada da variável `Amount`.

### Principais Conclusões (EDA)

![Gráfico de Densidade de Transações por Hora](reports/figures/grafico_das_fraudes_em_2_dias.jpg)

* **Padrão temporal:** existe uma concentração significativa de fraudes em horas de menor atividade normal (entre as 2h–3h e às 11h), sugerindo que as fraudes tendem a ocorrer em janelas temporais de menor monitorização.
* **Variáveis com maior poder discriminante:** V17, V14, V12 (correlação negativa com `Class`) e V11, V4 (correlação positiva).
* **Separabilidade visual:** a análise bivariada (V14 vs. V17) revela uma fronteira parcialmente clara entre fraudes e transações legítimas, indicando que modelos não-lineares serão capazes de aprender padrões úteis.

> Para detalhes completos das transformações e justificações técnicas, consultar [`docs/M2_exploracao.md`](docs/M2_exploracao.md).

---

## 3. Modelação (Milestone 3)

### Abordagem Técnica

* **Estratégia de Validação:** Divisão Treino/Teste 80/20 com `stratify=y` (preservação da proporção de fraudes), seguida de *Stratified K-Fold Cross-Validation* (K=5) para validação da estabilidade.
* **Modelos Testados:** Regressão Logística (*baseline*), Random Forest, XGBoost, XGBoost + SMOTE, XGBoost com hiperparâmetros sintonizados (*RandomizedSearchCV*).
* **Métricas Principais:** AUPRC (qualidade global em dados desbalanceados), Recall (sensibilidade na deteção de fraudes), Precision (qualidade dos alertas), F1-Score (equilíbrio).

### Modelo Final Selecionado: XGBoost com hiperparâmetros base

Configuração: `n_estimators=100`, `max_depth=6`, `scale_pos_weight=599`, `random_state=42`.

| Métrica | Resultado | Estado |
| :--- | :---: | :---: |
| AUPRC | 0.8251 | ✅ (Obj. 1: ≥ 0.80) |
| Recall | 0.7895 | ⚠️ (Obj. 2 original não cumprido — ver abaixo) |
| Precision | 0.9375 | ✅ |
| F1-Score | 0.8571 | — |
| Falsos Positivos (Teste) | 5 | — |
| Falsos Negativos (Teste) | 20 | — |

### Re-calibração do Critério de Sucesso

Durante a fase de experimentação, demonstrou-se que atingir o limiar de Recall ≥ 85% (Objetivo 2 original) exigia um *trade-off* operacionalmente insustentável: a Precision caía de 94% para 45%, com um aumento de 1.840% nos Falsos Positivos (de 5 para 97 transações legítimas bloqueadas). Esta evidência empírica levou à **re-calibração do critério de sucesso** para um objetivo composto que reflete o equilíbrio real exigido pelo negócio bancário:

> **Critério revisto:** Recall ≥ 75% **E** Precision ≥ 85% **E** AUPRC ≥ 0.80 — **todos cumpridos** pelo modelo final.

### Resposta à Pergunta de Investigação 4

A aplicação do SMOTE **não trouxe valor** neste contexto específico: todas as métricas pioraram face ao tratamento nativo de desequilíbrio via `scale_pos_weight` (Recall: 0.77 vs. 0.79; F1: 0.82 vs. 0.86; Falsos Positivos: 11 vs. 5). A explicação técnica completa desta descoberta encontra-se em [`docs/M3_modelacao.md`](docs/M3_modelacao.md).

> Para a documentação completa da fase de modelação, consultar [`docs/M3_modelacao.md`](docs/M3_modelacao.md).

---

## 4. Finalização (Milestone 4)

### Resposta ao Problema
*[A preencher após Milestone 4]*

### Recomendações de Inovação
*[A preencher após Milestone 4]*

---

## 5. Referências

1. Machine Learning Group — ULB & Worldline. (2018). *Credit Card Fraud Detection*. Kaggle Datasets. Disponível em: [https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud) (consultado em 15/04/2026).

2. Dal Pozzolo, A., Caelen, O., Johnson, R. A., & Bontempi, G. (2015). *Calibrating Probability with Undersampling for Unbalanced Classification*. IEEE Symposium Series on Computational Intelligence (SSCI). Disponível em: [https://doi.org/10.1109/SSCI.2015.33](https://doi.org/10.1109/SSCI.2015.33)

3. Dal Pozzolo, A., Boracchi, G., Caelen, O., Alippi, C., & Bontempi, G. (2018). *Credit Card Fraud Detection: A Realistic Modeling and a Novel Learning Strategy*. IEEE Transactions on Neural Networks and Learning Systems, 29(8), 3784–3797. Disponível em: [https://doi.org/10.1109/TNNLS.2017.2736643](https://doi.org/10.1109/TNNLS.2017.2736643)

4. Chawla, N. V., Bowyer, K. W., Hall, L. O., & Kegelmeyer, W. P. (2002). *SMOTE: Synthetic Minority Over-sampling Technique*. Journal of Artificial Intelligence Research, 16, 321–357. Disponível em: [https://doi.org/10.1613/jair.953](https://doi.org/10.1613/jair.953)

5. Chen, T., & Guestrin, C. (2016). *XGBoost: A Scalable Tree Boosting System*. Proceedings of the 22nd ACM SIGKDD International Conference on Knowledge Discovery and Data Mining, 785–794. Disponível em: [https://doi.org/10.1145/2939672.2939785](https://doi.org/10.1145/2939672.2939785)

6. Saito, T., & Rehmsmeier, M. (2015). *The Precision-Recall Plot Is More Informative than the ROC Plot When Evaluating Binary Classifiers on Imbalanced Datasets*. PLoS ONE, 10(3), e0118432. Disponível em: [https://doi.org/10.1371/journal.pone.0118432](https://doi.org/10.1371/journal.pone.0118432)

7. Bachmann, J. M. *Credit Fraud || Dealing with Imbalanced Datasets*. Kaggle Notebook. Disponível em: [https://www.kaggle.com/code/janiobachmann/credit-fraud-dealing-with-imbalanced-datasets](https://www.kaggle.com/code/janiobachmann/credit-fraud-dealing-with-imbalanced-datasets) (consultado em 15/04/2026).

> **Nota:** Esta lista será continuamente atualizada à medida que novas referências bibliográficas e científicas forem incorporadas no desenvolvimento do projeto.

> **Nota:** Esta lista será continuamente atualizada à medida que novas referências bibliográficas e científicas forem incorporadas no desenvolvimento do projeto.

---

## Como Reproduzir este Projeto

1. Clone o repositório: `git clone https://github.com/tomas15moreira/Projeto-Detecao-de-Transacoes-Fraudulentas-.git`
2. Instale as dependências: `pip install -r requirements.txt`
3. Execute os notebooks na pasta `notebooks/` seguindo a ordem numérica.

---

**Instituição:** Coimbra Business School | ISCAC
**Curso:** Licenciatura em Ciência de Dados para a Gestão
**Unidade Curricular:** Projeto em Ciência de Dados
**Professor Responsável:** Dora Melo (dmelo@iscac.pt)
