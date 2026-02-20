# Deteção de Transações Fraudulentas 

## Identificação do Grupo
* **Grupo nº: 8**
* **Membros:**
 * **João Freire (a2023128832)**
 * **Rodrigo Ferrão (a2022138105)** 
 * **Tomás Moreira (a2023143375)** 

## Organização do Repositório
A estrutura deste projeto segue as boas práticas de Ciência de Dados e Engenharia de Software:
* *data/*: Armazenamento de dados (dados brutos em raw/ e processados em processed/).
* *docs/*: Documentação técnica detalhada dividida por Milestones (M1, M2 e M3).
* *notebooks/*: Jupyter Notebooks para experimentação, limpeza e modelação.
* *src/*: Código-fonte modular (scripts .py) para funções reutilizáveis.
* *reports/*: Relatórios finais, apresentações e exportação de figuras (figures/).
* *requirements.txt*: Ficheiro de configuração com as bibliotecas necessárias.

## 1. Iniciação (Milestone 1)
### Contexto e Problema de Negócio
A fraude com cartões de crédito gera prejuízos financeiros massivos para bancos e consumidores. O desafio central é identificar transações fraudulentas em tempo real. No entanto, este é um problema de "procurar uma agulha no palheiro": a grande maioria das transações é legítima, tornando a deteção difícil sem gerar muitos alarmes falsos.

**O Desafio Técnico:** O dataset apresenta um desequilíbrio extremo (apenas 0.172% são fraudes), o que invalida métricas tradicionais como a "Acurácia".

### Objetivos do Projeto
* **Objetivo 1:** Atingir uma métrica AUPRC superior a 0.80 na identificação de fraudes, utilizando técnicas de oversampling (como SMOTE) para contornar o desequilíbrio de classes, até ao Milestone 3.
* **Objetivo 2:** Identificar as 5 variáveis com maior poder preditivo na deteção de fraude através de técnicas de Feature Importance, até à entrega da fase de modelação (Milestone 3).

### Fonte de Dados
* **Dataset:** [Credit Card Fraud Detection (Kaggle)](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud)
* **Dimensão:** 284.807 transações, 31 colunas.


## 2. Exploração (Milestone 2)
### Limpeza e Preparação
* [Breve resumo das ações de limpeza tomadas. Detalhes em `docs/M2_exploracao.md`]
### Principais Conclusões (EDA)
> *Dica: Insere aqui o gráfico mais importante do projeto.*
* **Ponto-chave:** [Ex: Identificámos que o fator X influencia em 40% o resultado Y, por aplicação
do método ganho de informação]
## 3. Modelação (Milestone 3)
### Abordagem Técnica
* **Modelos:** [Ex: Random Forest e XGBoost]
* **Métrica Principal:** [Ex: F1-Score ou RMSE]
## 4. Finalização (Milestone 4)
### Resposta ao Problema
[Resumo da solução e como ela gera valor para o negócio.]
### Recomendações de Inovação
1. [Sugestão prática baseada nos resultados]
## Como Reproduzir este Projeto
1. Clone o repositório: `git clone [url-do-repo]`
2. Instale as dependências: `pip install -r requirements.txt`
3. Execute os notebooks na pasta `notebooks/` seguindo a ordem numérica.
**Instituição:** Coimbra Business School | ISCAC
**Curso:** Licenciatura em Ciência de Dados para a Gestão
**Unidade Curricular:** Projeto em Ciência de Dados
**Professor Responsável:** Dora Melo (dmelo@iscac.pt)

