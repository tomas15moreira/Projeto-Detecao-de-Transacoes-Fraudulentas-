# Relatório de Conclusão e Entrega de Valor (Milestone 4)
## 1. Síntese de Resultados e Impacto
> **Tradução de métricas técnicas em impacto financeiro e operacional.**

* **O Problema Resolvido:** O objetivo traçado no Milestone 1 foi o de desenvolver um modelo preditivo capaz de identificar transações fraudulentas num cenário de extremo desequilíbrio (apenas 0,17% de fraudes), minimizando as perdas financeiras sem causar atrito aos clientes. O objetivo foi plenamente alcançado: o modelo XGBoost final atingiu uma métrica AUPRC de 0.8251 (superando a meta SMART de ≥ 0.80) e um _Recall_ médio de 85,98% em validação cruzada.
* **Interpretação dos Resultados:** Em linguagem simples, o modelo consegue identificar quase 80% de todas as fraudes ativas nos dados de teste com uma taxa de precisão de 94%. Isto significa que quando o sistema emite um alerta e bloqueia um cartão, há 94% de certeza estatística de que é um ataque real, provando ser uma ferramenta altamente cirúrgica em vez de um sistema de alarmes falsos.
* **Valor para o Negócio:** A tradução destas métricas para impacto financeiro demonstra um valor prático extraordinário. Apenas no conjunto de teste, o modelo protegeu quase 11.000€ (74,3% do valor total em risco), o dobro do valor assegurado pelo modelo de referência (_Baseline_). Numa simulação de escala bancária real (500.000 transações/dia), este modelo protegeria mais de 37,4 Milhões de Euros por ano. O atrito operacional provou ser quase nulo: o sistema geraria apenas 44 bloqueios indevidos por dia, um volume perfeitamente gerível por uma pequena equipa de apoio ao cliente, sem necessidade de aumentar os custos operacionais do banco.

## 2. Análise Crítica e Limitações
> **Nota:** Identificar de forma honesta as fronteiras do projeto e onde o modelo pode falhar.
* **Limitações dos Dados:** * (Ex: "O volume de dados para a classe X era reduzido, o que pode
afetar a precisão em cenários específicos.")
* **Limitações do Modelo:** * (Ex: "O modelo de associação foca-se em relações de co-ocorrência,
mas não prova causalidade direta entre as variáveis.")
* **Contextos de Falha:** * (Ex: "O modelo não é recomendado para situações de [Cenário Y], uma
vez que os dados de treino não contemplavam essa variável externa.")
## 3. Considerações Éticas e de Viés
* **Privacidade:** (Ex: "Todos os identificadores pessoais foram removidos, garantindo que o
modelo analisa apenas padrões de comportamento anónimos.")
* **Transparência:** (Ex: "Utilizámos técnicas de 'Feature Importance' para garantir que as
decisões do modelo são explicáveis e não operam como uma 'caixa negra'.")
## 4. Roadmap e Trabalhos Futuros
> **Nota:** Sugestões concretas para quem quiser continuar ou escalar este projeto.
1. **Melhoria Técnica:** (Ex: "Implementar técnicas de reamostragem (SMOTE) para lidar melhor
com o desequilíbrio das classes.")
2. **Novas Variáveis:** (Ex: "Integrar dados meteorológicos ou sazonais para refinar as previsões
de venda.")
3. **Escalabilidade (Deployment):** (Ex: "Desenvolver uma interface web (Streamlit) para que o
modelo possa ser utilizado por utilizadores não-técnicos em tempo real.")
---
**Data de Conclusão:** [Inserir Data]  
**Versão do Projeto:** v4.0 Final--- 
