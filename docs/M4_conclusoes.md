# Relatório de Conclusão e Entrega de Valor (Milestone 4)
## 1. Síntese de Resultados e Impacto
> **Tradução de métricas técnicas em impacto financeiro e operacional.**

* **O Problema Resolvido:** O objetivo traçado no Milestone 1 foi o de desenvolver um modelo preditivo capaz de identificar transações fraudulentas num cenário de extremo desequilíbrio (apenas 0,17% de fraudes), minimizando as perdas financeiras sem causar atrito aos clientes. O objetivo foi plenamente alcançado: o modelo XGBoost final atingiu uma métrica AUPRC de 0.8251 (superando a meta SMART de ≥ 0.80) e um _Recall_ médio de 85,98% em validação cruzada.
* **Interpretação dos Resultados:** Em linguagem simples, o modelo consegue identificar quase 80% de todas as fraudes ativas nos dados de teste com uma taxa de precisão de 94%. Isto significa que quando o sistema emite um alerta e bloqueia um cartão, há 94% de certeza estatística de que é um ataque real, provando ser uma ferramenta altamente cirúrgica em vez de um sistema de alarmes falsos.
* **Valor para o Negócio:** A tradução destas métricas para impacto financeiro demonstra um valor prático extraordinário. Apenas no conjunto de teste, o modelo protegeu quase 11.000€ (74,3% do valor total em risco), o dobro do valor assegurado pelo modelo de referência (_Baseline_). Numa simulação de escala bancária real (500.000 transações/dia), este modelo protegeria mais de 37,4 Milhões de Euros por ano. O atrito operacional provou ser quase nulo: o sistema geraria apenas 44 bloqueios indevidos por dia, um volume perfeitamente gerível por uma pequena equipa de apoio ao cliente, sem necessidade de aumentar os custos operacionais do banco.

## 2. Análise Crítica e Limitações
> **Identificação honesta das fronteiras e vulnerabilidades do projeto.**

* **Limitações dos Dados (Anonimização PCA):** A limitação estrutural mais profunda deste projeto reside na natureza dos dados fornecidos. Devido a exigências estritas de confidencialidade bancária, 28 das 30 variáveis preditivas (V1 a V28) foram ofuscadas através de uma Análise de Componentes Principais (PCA). Embora isto proteja a privacidade dos clientes, sacrifica drasticamente a interpretabilidade de negócio. Por exemplo, sabemos matematicamente que a componente `V14` é a variável mais importante para detetar a fraude, mas é impossível traduzir o "V14" para uma regra de negócio tangível (como "país da transação", "tipo de comerciante" ou "antiguidade do cartão").
* **Limitações do Modelo (O Ponto Cego das Micro-Transações):** A auditoria às 20 fraudes que o modelo não conseguiu detetar (Falsos Negativos) revelou um padrão estatístico muito claro: o modelo tem extrema dificuldade em identificar fraudes de baixo valor monetário. Das fraudes que escaparam, 60% (12 casos) referiam-se a transações inferiores a 5€, apresentando uma mediana de apenas 2,00€. No contexto da cibercriminalidade bancária, este padrão sugere a prática de _Card Testing_, onde os criminosos efetuam transações ínfimas apenas para verificar se um cartão roubado está ativo antes de efetuarem o ataque principal. Como estas micro-transações não causam qualquer perturbação estatística no perfil de consumo, o modelo acaba por classificá-las como legítimas.
* **Contextos de Falha (Zonas Cinzentas Estatísticas):** O modelo perde fiabilidade em ataques de "baixa intensidade" que mimetizam perfeitamente o comportamento habitual do cliente. Se um ataque não cria um desvio estatístico nas componentes PCA nem ocorre num padrão horário invulgar, o algoritmo é forçado a classificá-lo como transação normal para evitar o bloqueio abundante de clientes autênticos. A incapacidade de cruzar a transação com variáveis contextuais externas (como uma discrepância entre o endereço IP da compra e a localização GPS habitual do telemóvel do cliente) impede o modelo de separar perfeitamente as classes nesta zona de sobreposição.

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
