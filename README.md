# Detecção de Fraude em Cartões de Crédito 💳

Projeto de ciência de dados para detectar transações fraudulentas no dataset público de cartões de crédito. O [notebook executável, com células e saídas salvas](deteccao_fraude_cartao_credito.ipynb) também pode ser aberto no [Google Colab](https://colab.research.google.com/drive/1iHpy80wBIrCUQQxLJwcmsLcJ9f293aeU?usp=sharing).

## Problema e desbalanceamento

Fraudes correspondem a apenas **0,1727%** das 284.807 transações. Acurácia isolada é enganosa: prever tudo como legítimo alcançaria aproximadamente **99,83%**, mas não detectaria fraude. Por isso, a comparação prioriza precisão, recall e F1 da classe fraude; a curva Precision-Recall ajuda a avaliar modelos sem ser dominada pela classe majoritária.

## Preparação dos dados

- O CSV é carregado pelo próprio notebook de `https://storage.googleapis.com/download.tensorflow.org/data/creditcard.csv`. O dataset não está armazenado no repositório.
- `Time` é removida; `Amount` é transformada em `Log_Amount = log1p(Amount)` e a coluna original `Amount` é descartada.
- A divisão é estratificada: 30% para teste; os 70% restantes são separados em treino (80%) e validação (20%). Assim, a divisão final é 56% treino, 14% validação e 30% teste.
- O `StandardScaler` é ajustado somente no treino e aplicado à validação e ao teste, evitando vazamento de dados.

## Comparação de modelos

Os resultados abaixo são do teste, usando o limiar padrão **0,5** para todos os modelos. Regressão Logística e Random Forest usam pesos de classe; XGBoost usa `scale_pos_weight`. **PR AUC** é a área trapezoidal da curva Precision-Recall; **Average Precision (AP)** é reportada separadamente, pois usa outra agregação.

| Modelo | Precisão (fraude) | Recall (fraude) | F1 (fraude) | PR AUC trapezoidal | AP |
| :--- | :---: | :---: | :---: | :---: | :---: |
| Regressão Logística | 0,0619 | 0,8784 | 0,1157 | 0,7464 | 0,7093 |
| Random Forest | 0,8527 | 0,7432 | 0,7942 | 0,7719 | 0,7724 |
| XGBoost | 0,8864 | 0,7905 | 0,8357 | 0,8334 | 0,8335 |

## Limiar de decisão

Para o XGBoost, selecionei o limiar que maximiza o F1 **na validação**, sem usar o teste para otimizar. O limiar encontrado foi **0,883833** (F1 de validação: 0,8976). No teste, esse limiar obteve **precisão 0,9262**, **recall 0,7635** e **F1 0,8370**. Em comparação ao limiar padrão 0,5, aumenta a precisão e reduz um pouco o recall; a escolha reflete esse compromisso e deve ser ajustada conforme o custo operacional de falsos positivos e fraudes não detectadas.

## O que SHAP mostrou

A explicação SHAP foi calculada para o **Random Forest**, em uma amostra de 1.000 transações do teste. Pela média do valor absoluto de SHAP, os quatro atributos mais influentes foram **V4 (0,0707), V14 (0,0692), V12 (0,0669) e V10 (0,0534)**, seguidos por V11. O gráfico beeswarm salvo no notebook mostra a direção e a dispersão das contribuições para a classe fraude. SHAP descreve a influência do modelo, não causalidade.

## O que foi ampliado em relação ao exercício inicial

Em relação ao exemplo inicial da Expert neste notebook (dados sintéticos e Isolation Forest), ampliei o projeto para o dataset real rotulado e acrescentei preparação de dados sem vazamento, comparação de três classificadores supervisionados com tratamento do desbalanceamento, métricas e curva Precision-Recall, seleção do limiar em validação e explicabilidade SHAP.

## Execução

Abra o notebook no Google Colab ou em Jupyter. Ele carrega os dados pela URL, treina os modelos e contém saídas salvas, relatórios de métricas e gráficos. A célula SHAP instala essa dependência quando executada.
