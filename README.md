# Previsão de Fluxo Solar F10.7 com Machine Learning

Projeto acadêmico da **Global Solution — FIAP**, que aplica técnicas de **regressão supervisionada** para estimar o indicador de atividade solar **F10.7** (fluxo de rádio solar em 10,7 cm) a partir de outros índices observados de atividade do Sol.

O estudo relaciona análise de dados e Machine Learning a um problema da **economia espacial**: a atividade solar afeta a densidade da alta atmosfera e, consequentemente, o arrasto experimentado por satélites em órbita baixa.

> **Escopo:** trata-se de uma estimativa tabular a partir de outros índices solares do mesmo conjunto de observações, e **não de uma previsão operacional de atividade solar futura**. A aplicação a operações de satélites é uma motivação do estudo, não uma implantação realizada.

## Visão geral

| Item | Descrição |
| --- | --- |
| Problema | Regressão para estimar `Radio Flux 10.7cm` |
| Dados | Dataset de índices solares e geomagnéticos, arquivo `daily_solar_data.csv` |
| Amostras utilizadas | **10.239** (8.191 para treinamento e 2.048 para teste) |
| Modelos avaliados | Regressão Linear, KNN Regressor e Árvore de Decisão |
| Métricas | MAE, RMSE e R² |
| Ferramentas | Python, pandas, NumPy, scikit-learn, Matplotlib, Seaborn |
| Entrega principal | [Notebook com análise e modelos](./solar_f107_ml_FINAL.ipynb) |

## Problema e contexto

O fluxo de rádio solar **F10.7** é um indicador relacionado à atividade do Sol e é utilizado em modelos que ajudam a estudar as condições da alta atmosfera. Em altitudes orbitais baixas, variações nessas condições podem influenciar o arrasto sobre satélites.

Neste exercício, a pergunta é: **até que ponto outros indicadores solares permitem estimar o F10.7 em um conjunto de dados tabular?**

O projeto não prevê diretamente falhas, colisões, órbitas nem demanda futura de satélites. Ele explora as relações estatísticas entre variáveis físicas observadas.

## Etapas do notebook

### 1. Carregamento e exploração dos dados

Foram examinados tipos de variáveis, estatísticas descritivas, distribuições e relações entre os índices solares e o alvo.

O notebook registra correlação de aproximadamente **0,93** entre o número de manchas solares e o F10.7, além de cerca de **0,88** entre a área de manchas e o F10.7. Essas relações ajudam a interpretar o desempenho dos modelos.

### 2. Tratamento e preparação

O dataset contém marcadores de ausência de medição que não aparecem inicialmente como valores nulos convencionais:

- `*` em valores textuais;
- `-1` em colunas de flares.

Esses marcadores são convertidos em `NaN`; o notebook também remove uma coluna de classificação textual de raios X e a coluna de data, tratando o problema como **regressão tabular**, não como série temporal.

Os dados são divididos em **80% treino / 20% teste** (semente `42`). `StandardScaler` é aplicado aos dados de Regressão Linear e KNN, com ajuste no conjunto de treinamento. Para Árvore de Decisão, são usados os atributos sem padronização.

### 3. Modelagem e análise dos resultados

Os três modelos implementados são:

- **Regressão Linear:** modelo linear utilizado como referência interpretável;
- **KNN Regressor:** compara observações semelhantes com base em distâncias;
- **Árvore de Decisão:** utiliza partições sucessivas dos atributos para estimar o alvo.

O notebook também compara valores de `k` do KNN e profundidades da árvore, ilustrando **overfitting** e **underfitting** ao observar o desempenho em treino e teste.

## Resultados registrados

Resultados apresentados na execução salva do notebook:

| Modelo | MAE ↓ | RMSE ↓ | R² ↑ |
| --- | ---: | ---: | ---: |
| **Regressão Linear** | **8,9281** | **13,0497** | **0,9037** |
| KNN (k=5) | 9,8007 | 14,9533 | 0,8735 |
| Árvore (profundidade máxima = 6) | 9,0767 | 13,5076 | 0,8968 |

**Modelo escolhido:** Regressão Linear, que apresentou o melhor R² e os menores erros entre essas três configurações avaliadas, com uma estrutura relativamente fácil de interpretar.

A análise inclui gráficos de **valores reais versus previstos**, **resíduos**, **R² versus número de vizinhos** e **R² versus profundidade da árvore**.

### Interpretação e limitações

- Um **R² de aproximadamente 0,904** mostra bom ajuste no conjunto de teste utilizado, mas não comprova desempenho em dados futuros.
- Como os dados têm dimensão temporal, a separação aleatória pode produzir uma estimativa mais favorável que uma avaliação prospectiva. Uma evolução importante seria **dividir treino e teste cronologicamente** e testar o desempenho em períodos posteriores.
- A correlação elevada entre determinados indicadores solares e o F10.7 contribui para a capacidade de estimativa, mas não estabelece causalidade.
- A comparação de vários hiperparâmetros no próprio teste pode influenciar a escolha de configuração; uma etapa de validação separada ou validação temporal seria mais rigorosa.
- As conclusões são de um exercício acadêmico, não de um sistema validado para tomada de decisão orbital real.

## Como executar

1. Abra [`solar_f107_ml_FINAL.ipynb`](./solar_f107_ml_FINAL.ipynb) em **Jupyter Notebook**, **JupyterLab**, VS Code ou Google Colab.
2. Garanta que o arquivo [`daily_solar_data.csv`](./daily_solar_data.csv) esteja no diretório de execução do notebook.
3. Instale as dependências usadas:

```bash
python -m pip install pandas numpy matplotlib seaborn scikit-learn jupyter
```

4. Execute as células em ordem e examine os gráficos e a tabela de métricas.

> O repositório não fixa versões das bibliotecas, e os resultados apresentados são os registrados no notebook, não uma reexecução independente neste README.

## Arquivos

```text
.
├── solar_f107_ml_FINAL.ipynb  # Análise de dados, treinamento e avaliação
├── daily_solar_data.csv      # Dados utilizados
├── GS_ClimaEspacial_Slides.html  # Slides do trabalho em HTML
├── README.md
├── LICENSE
└── Video                      # Referência a material em vídeo
```

## Integrantes

Projeto acadêmico desenvolvido em equipe:

- Felipe Piccolo
- Leonardo Bueno
- João Vitor Veiga

---

**Principal aprendizado:** comparar modelos e métricas é tão importante quanto obter um bom resultado numérico. A análise de erros, a interpretação física das variáveis e a escolha de uma validação adequada ajudam a evitar conclusões excessivamente otimistas.
