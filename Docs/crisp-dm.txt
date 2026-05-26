# Análise CRISP-DM — Modelo de Scoring de Propostas

## 1. Introdução

Este projeto tem como objetivo apoiar a tomada de decisão comercial a partir de um modelo de Machine Learning capaz de estimar a probabilidade de uma proposta ser contratada.

No cenário atual, o negócio possui um grande volume de propostas simuladas, porém apenas uma pequena parcela se converte em contrato. Isso cria um desafio operacional e comercial: identificar quais propostas possuem maior chance de conversão para que os esforços de atendimento, priorização e relacionamento sejam direcionados de forma mais eficiente.

O modelo não deve ser visto como uma decisão final automática, mas como uma ferramenta de apoio para priorização de oportunidades.

---

## 2. Problema de negócio

O negócio precisa responder à seguinte pergunta:

**Entre todas as propostas disponíveis, quais possuem maior probabilidade de contratação?**

A partir dessa resposta, a área comercial pode:

- priorizar leads/propostas com maior chance de conversão;
- reduzir esforço em propostas com baixa propensão;
- melhorar a eficiência operacional;
- apoiar campanhas e estratégias comerciais;
- identificar padrões de clientes com maior aderência ao produto.

O erro mais caro para o negócio é classificar uma boa proposta como baixa prioridade, pois isso pode fazer a empresa perder clientes com real potencial de contratação.

---

## 3. Objetivo do modelo

O objetivo do modelo é prever a probabilidade de uma proposta ser contratada.

A variável alvo do modelo é:

- `1`: contratou;
- `0`: não contratou.

Portanto, trata-se de um problema de classificação binária.

O resultado esperado não é apenas dizer se uma proposta será ou não contratada, mas gerar uma probabilidade que permita ordenar as propostas da maior para a menor chance de conversão.

Exemplo:

| Proposta | Probabilidade de contratação | Prioridade |
|---|---:|---|
| A | 82% | Alta |
| B | 61% | Média |
| C | 18% | Baixa |

Essa visão permite transformar o modelo em uma ferramenta prática de decisão para o gestor.

---

## 4. Dataset

O dataset utilizado possui mais de 5.000 propostas e 33 colunas com diferentes tipos de informações. A base apresenta forte desbalanceamento: aproximadamente 6,7% das propostas foram contratadas, enquanto 93,3% não foram contratadas.

Esse ponto é importante porque o modelo pode tender a aprender melhor a classe majoritária, ou seja, propostas que não contrataram. Por isso, a avaliação do modelo não deve depender apenas da acurácia.

### Variável identificadora

A variável de identificação sugerida é:

- `id_proposta`

Essa variável deve ser usada apenas para rastrear a proposta e não deve entrar como variável explicativa do modelo.

### Variável alvo

- `contratou`

### Features consideradas

As principais variáveis candidatas para o modelo são:

- valor do imóvel;
- valor financiado;
- entrada;
- LTV;
- idade;
- renda mensal;
- score de crédito;
- comprometimento de renda;
- possui FGTS;
- valor da parcela;
- cidade;
- UF;
- região;
- tempo de relacionamento;
- número de consultas de crédito;
- prazo;
- taxa;
- estado civil.

### Variáveis que exigem cuidado

Algumas variáveis precisam ser avaliadas antes de entrar no modelo, principalmente para evitar vazamento de informação.

Não devem ser usadas variáveis que só existem depois da contratação, como:

- data de contratação;
- status final da proposta;
- qualquer informação criada após a decisão final.

Também é necessário avaliar com cuidado variáveis como “aprovado na pré-análise”, pois elas podem carregar uma decisão anterior e influenciar artificialmente o desempenho do modelo.

---

## 5. Metodologia CRISP-DM

O projeto foi estruturado seguindo a metodologia CRISP-DM, que organiza um projeto de dados em etapas orientadas ao negócio.

### 5.1 Business Understanding

Nesta etapa, foi definido que o objetivo principal do modelo é apoiar a priorização comercial das propostas.

As principais perguntas de negócio são:

- Qual proposta tem maior chance de contratação?
- Quais variáveis influenciam mais a conversão?
- Qual erro é mais caro para o negócio?
- Qual ponto de corte deve ser usado para classificar uma proposta como prioritária?

O sucesso do modelo será medido pela capacidade de separar melhor propostas com maior e menor probabilidade de contratação.

### 5.2 Data Understanding

A análise inicial mostrou que a base possui forte desbalanceamento entre contratos e não contratos.

Foram identificadas aproximadamente 335 propostas contratadas em uma base com mais de 5.000 registros, representando cerca de 6,7% de conversão.

Esse comportamento indica que o evento de interesse é raro, o que torna métricas como recall, precision, AUC e matriz de confusão mais importantes do que a acurácia isolada.

### 5.3 Data Preparation

A preparação dos dados deve considerar:

- tratamento de valores ausentes;
- separação entre variáveis numéricas e categóricas;
- padronização de variáveis numéricas;
- codificação de variáveis categóricas;
- remoção de variáveis com vazamento de informação;
- separação entre treino e teste;
- preservação do identificador da proposta fora do treinamento.

Como foi utilizada Regressão Logística, a aplicação de scaler é importante, pois o modelo é sensível à escala das variáveis.

### 5.4 Modeling

O modelo inicial escolhido foi a Regressão Logística.

Essa escolha faz sentido para uma primeira versão porque:

- é interpretável;
- permite avaliar o peso das variáveis;
- gera probabilidades;
- é adequada para problemas de classificação binária;
- permite uma boa explicação para áreas de negócio.

O modelo retorna a probabilidade estimada de contratação para cada proposta.

### 5.5 Evaluation

A avaliação foi feita considerando métricas adequadas para um problema desbalanceado:

- ROC/AUC;
- recall;
- precision;
- threshold;
- matriz de confusão.

A análise não deve depender apenas da acurácia, porque a base possui muito mais propostas não contratadas do que contratadas.

### 5.6 Deployment

A aplicação prática do modelo pode ser feita por meio de uma tabela final com:

- ID da proposta;
- probabilidade de contratação;
- faixa de prioridade;
- principais variáveis explicativas;
- recomendação de abordagem comercial.

Exemplo:

| Proposta | Probabilidade | Prioridade | Ação sugerida |
|---|---:|---|---|
| 001 | 85% | Alta | Contato comercial imediato |
| 002 | 63% | Média | Nutrição/retomada |
| 003 | 22% | Baixa | Baixa prioridade |

---

## 6. Análise Exploratória

### 6.1 Distribuição da variável alvo

A base possui um forte desbalanceamento entre propostas contratadas e não contratadas.

- Contrataram: aproximadamente 6,7%;
- Não contrataram: aproximadamente 93,3%.

Esse comportamento mostra que a contratação é um evento menos frequente dentro da base.

Para o negócio, isso reforça a importância de priorizar corretamente as propostas, pois abordar todas com a mesma intensidade pode gerar desperdício de esforço comercial.

### 6.2 Score de crédito

A análise indicou que a média do score de crédito é maior entre os clientes que contrataram.

- Média do score dos contratados: aproximadamente 690;
- Média do score dos não contratados: aproximadamente 569.

Isso sugere que o score de crédito possui relação relevante com a contratação.

Porém, o score sozinho não explica completamente a conversão. Existem clientes com score alto que não contrataram e clientes com score dentro da média dos não contratantes que podem ter convertido.

Conclusão de negócio: o score é uma variável importante, mas deve ser analisado junto com renda, LTV, entrada, valor financiado, comprometimento de renda e demais características da proposta.

### 6.3 Conversão por região

A taxa de conversão por região apresentou variações moderadas:

| Região | Taxa de conversão | Total de propostas | Renda média |
|---|---:|---:|---:|
| Norte | 7,85% | 522 | 9.336 |
| Centro-Oeste | 6,98% | 530 | 10.194 |
| Sul | 6,82% | 718 | 9.743 |
| Nordeste | 6,71% | 998 | 9.236 |
| Sudeste | 6,32% | 2.232 | 9.576 |

A Região Norte apresentou a maior taxa percentual de conversão, enquanto o Sudeste concentrou o maior volume absoluto de propostas.

Conclusão de negócio: o Sudeste gera mais volume, mas não necessariamente maior eficiência proporcional. Isso pode indicar oportunidade de revisão na abordagem comercial, segmentação regional ou qualificação de propostas.

### 6.4 Idade e prazo

As médias de idade e prazo entre contratados e não contratados ficaram próximas:

| Contratou | Idade média | Prazo médio | Anos |
|---|---:|---:|---:|
| 0 | 38,97 | 331,90 | 27,66 |
| 1 | 37,46 | 343,34 | 28,61 |

A diferença não parece suficientemente grande para afirmar que idade e prazo, isoladamente, separam fortemente os grupos.

Conclusão de negócio: idade e prazo podem contribuir para o modelo, mas provavelmente não são os principais fatores de decisão quando analisados sozinhos.

### 6.5 Valor médio dos imóveis por região

| Região | Valor médio do imóvel |
|---|---:|
| Norte | 312.103 |
| Centro-Oeste | 425.361 |
| Sul | 415.837 |
| Nordeste | 329.517 |
| Sudeste | 417.897 |
| Média geral | 380.143 |

As diferenças existem, mas não indicam, isoladamente, um viés extremo no modelo.

Conclusão de negócio: região, valor do imóvel e renda devem ser analisados em conjunto, pois o poder de compra e o perfil de financiamento podem variar conforme o mercado regional.

---

## 7. Modelagem

### 7.1 Modelo utilizado

O modelo utilizado foi a Regressão Logística.

A Regressão Logística estima a probabilidade de uma proposta pertencer à classe positiva, neste caso, `contratou = 1`.

A saída do modelo pode ser interpretada como uma pontuação de propensão à contratação.

### 7.2 Scaler

Foi utilizado scaler nas variáveis numéricas.

Isso é importante porque variáveis como renda mensal, valor do imóvel, valor financiado, score de crédito e idade possuem escalas muito diferentes.

Sem padronização, variáveis com valores numericamente maiores podem influenciar o modelo de forma desproporcional.

### 7.3 Threshold

O threshold é o ponto de corte usado para transformar a probabilidade em classificação.

Exemplo:

- se o threshold for 0,50, propostas com probabilidade acima de 50% são classificadas como potenciais contratantes;
- se o threshold for 0,30, o modelo passa a considerar mais propostas como potenciais contratantes;
- se o threshold for 0,70, o modelo fica mais restritivo.

O threshold impacta diretamente o equilíbrio entre recall e precision.

### 7.4 Recall e precision

O recall mede a capacidade do modelo de encontrar os clientes que realmente contratariam.

Em termos de negócio, um recall maior significa reduzir a chance de deixar bons clientes fora da priorização.

A precision mede, entre as propostas classificadas como boas, quantas realmente tinham maior aderência à contratação.

Em termos de negócio, uma precision maior significa que o time comercial terá menos desperdício de esforço.

O recall varia bastante de acordo com o threshold selecionado. Porém, ao aumentar o recall, geralmente a precision pode cair. Da mesma forma, ao buscar maior precision, o recall pode diminuir.

Essa decisão não deve ser puramente técnica. O ponto de corte ideal precisa ser definido junto com os gestores do negócio, pois depende da estratégia comercial:

- se o objetivo for não perder bons clientes, o negócio pode aceitar maior recall;
- se o objetivo for reduzir esforço comercial e abordar menos propostas, o negócio pode priorizar maior precision;
- se houver capacidade operacional limitada, o threshold deve respeitar o volume que o time consegue trabalhar.

---

## 8. Resultados

### 8.1 ROC/AUC

O modelo apresentou AUC de 0,80.

Isso significa que o modelo tem aproximadamente 80% de chance de atribuir uma probabilidade maior para uma proposta que realmente contratou do que para uma proposta que não contratou.

Na prática, o modelo consegue separar razoavelmente bem clientes com maior e menor propensão à contratação.

Um AUC de 0,80 indica bom poder discriminatório para uma primeira versão do modelo, principalmente considerando o desbalanceamento da base.

### 8.2 Interpretação do resultado

A curva ROC não ficou extremamente próxima do canto superior esquerdo, portanto o modelo ainda não é perfeito.

Mesmo assim, o desempenho indica que ele aprendeu padrões relevantes nos dados e pode ser usado como base inicial para priorização comercial.

O modelo não deve ser interpretado como uma ferramenta que acerta todos os contratos, mas como um mecanismo para ordenar oportunidades por probabilidade.

### 8.3 Trade-off entre recall e precision

O principal ponto de decisão não é apenas o AUC, mas sim o threshold que será usado na operação.

Cada threshold gera um comportamento diferente:

| Estratégia | Efeito esperado | Risco |
|---|---|---|
| Threshold mais baixo | Captura mais potenciais contratantes | Mais falsos positivos |
| Threshold mais alto | Seleciona menos propostas, com maior qualidade média | Pode perder bons clientes |
| Threshold intermediário | Equilibra volume e qualidade | Exige validação operacional |

A decisão final sobre o threshold deve considerar a capacidade do time comercial, o custo de abordagem e o custo de perder uma proposta com alto potencial.

---

## 9. Conclusão de negócio

O modelo apresenta potencial relevante para apoiar a priorização comercial de propostas.

A principal entrega de valor não está apenas em prever se uma proposta será contratada ou não, mas em criar uma fila inteligente de priorização baseada em probabilidade de conversão.

Com o modelo, o gestor deixa de olhar apenas para volume bruto de propostas e passa a enxergar quais oportunidades merecem mais atenção.

A análise mostrou que variáveis como score de crédito, renda, LTV, valor financiado, entrada, comprometimento de renda e características regionais podem ajudar a explicar a probabilidade de contratação.

O AUC de 0,80 indica que o modelo possui boa capacidade de separação entre propostas com maior e menor propensão. Isso permite transformar a análise em uma ferramenta prática para direcionamento comercial.

Do ponto de vista de negócio, o modelo pode gerar valor em três frentes principais:

1. **Priorização comercial**  
   Direcionar o time para propostas com maior chance de contratação.

2. **Eficiência operacional**  
   Reduzir esforço em propostas com baixa probabilidade de conversão.

3. **Inteligência estratégica**  
   Identificar padrões de clientes, regiões e características de propostas com melhor desempenho.

A definição do threshold deve ser feita em conjunto com os gestores, porque ela representa uma escolha de negócio. Se a empresa quiser capturar o máximo de clientes possíveis, deve priorizar recall. Se quiser reduzir desperdício operacional, deve priorizar precision.

Portanto, o modelo não substitui a decisão comercial. Ele organiza melhor as oportunidades para que o negócio decida onde concentrar esforço.

---

## 10. Próximos passos

Para evoluir o projeto, os próximos passos recomendados são:

### 10.1 Feature engineering

Criar novas variáveis que possam melhorar a explicação do comportamento de contratação, como:

- relação entre parcela e renda;
- faixa de LTV;
- faixa de score;
- faixa de renda;
- diferença entre valor do imóvel e valor financiado;
- idade ao final do financiamento;
- combinação entre região, renda e valor do imóvel;
- indicadores de capacidade de pagamento.

### 10.2 Testar modelos ensemble

Após a baseline com Regressão Logística, testar modelos mais robustos:

- Random Forest;
- XGBoost;
- LightGBM;
- CatBoost.

Esses modelos podem capturar relações não lineares e interações entre variáveis.

### 10.3 Tuning de hiperparâmetros

Realizar otimização dos parâmetros dos modelos para buscar melhor equilíbrio entre recall, precision e AUC.

### 10.4 Tratamento do desbalanceamento

Avaliar técnicas para lidar com a baixa proporção de contratos, como:

- ajuste de `class_weight`;
- undersampling;
- oversampling;
- SMOTE;
- avaliação por faixas de probabilidade.

### 10.5 Segmentação

Criar análises segmentadas por:

- região;
- faixa de renda;
- faixa de score;
- LTV;
- valor do imóvel;
- perfil de entrada;
- prazo.

Isso pode gerar estratégias comerciais diferentes para cada grupo.

### 10.6 Validação com negócio

Levar os resultados para os gestores e validar:

- qual volume de propostas o time consegue abordar;
- qual erro é mais aceitável;
- qual erro é mais caro;
- qual threshold faz sentido operacionalmente;
- quais faixas de prioridade devem ser usadas.

### 10.7 Deploy analítico

Criar uma saída final consumível pelo negócio, como:

- planilha ranqueada;
- dashboard em Power BI;
- API simples;
- rotina mensal/semanal de scoring;
- relatório executivo com faixas de prioridade.

---

## 11. Recomendação final

A recomendação é usar este modelo como uma primeira camada de inteligência para priorização de propostas.

A versão atual já indica capacidade relevante de separação, mas ainda deve ser validada operacionalmente antes de ser usada como regra definitiva.

O melhor uso do modelo é transformar a probabilidade de contratação em uma régua de ação comercial:

| Faixa | Probabilidade | Ação |
|---|---:|---|
| Alta prioridade | Acima do threshold definido | Contato imediato |
| Média prioridade | Faixa intermediária | Nutrição e acompanhamento |
| Baixa prioridade | Baixa probabilidade | Baixa intensidade comercial |

Dessa forma, o modelo deixa de ser apenas uma análise técnica e passa a ser uma ferramenta de apoio à decisão para o negócio.
