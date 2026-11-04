# Detecção de Fraude em Cartões de Crédito 💳

Este repositório apresenta um projeto fim-a-fim de ciência de dados focado na detecção de transações fraudulentas usando dados reais de cartão de crédito. O principal desafio técnico abordado aqui é o **desbalanceamento extremo das classes**, onde apenas **0,17%** de todas as transações correspondem a fraudes.

## 🔍 O Problema de Negócio & Desbalanceamento
No cenário de prevenção a fraudes financeiras, a acurácia global é uma métrica enganosa. Se criarmos um classificador ingênuo que sempre prevê uma transação como legítima (Classe 0), ele obterá uma acurácia de **99,83%**. Contudo, esse modelo não impedirá nenhuma perda financeira.

Por essa razão, o foco deste projeto está voltado às métricas de:
* **Recall (Sensibilidade):** A proporção de fraudes reais que conseguimos detectar.
* **Precisão (Precision):** A proporção de alarmes que são fraudes reais (evitando frustrar o cliente legítimo com bloqueios indevidos).
* **F1-Score:** O equilíbrio harmônico entre precisão e recall.
* **PR AUC (Área Sob a Curva Precision-Recall):** A métrica de ouro para comparar modelos em dados muito desbalanceados.

---

## 🛠️ Preparação dos Dados
Os dados brutos consistem em transações financeiras reais anonimizadas via PCA (V1 a V28), com exceção das colunas `Time` e `Amount` (Valor).
1. **Tratamento de Outliers:** Aplicamos a transformação logarítmica na variável `Amount` (`Log_Amount = log1p(Amount)`) para suavizar o impacto de valores de transações extremamente altos e distorcidos.
2. **Padronização:** Utilizamos o `StandardScaler` do *scikit-learn* para colocar todos os recursos na mesma escala.
3. **Divisão Estratificada:** Dividimos os dados em 70% para treino e 30% para teste, garantindo que a raríssima proporção de fraudes ($0,17\%$) fosse mantida perfeitamente em ambos os conjuntos (`stratify=y`).

---

## 📊 Comparação entre Modelos

Para resolver o desbalanceamento, aplicamos abordagens focadas em pesos de classes (`class_weight` e `scale_pos_weight`). Os resultados obtidos no conjunto de testes foram:

| Modelo | Precisão (Fraude) | Recall (Fraude) | F1-Score (Fraude) | PR AUC (Métrica de Ouro) |
| :--- | :---: | :---: | :---: | :---: |
| **Regressão Logística** *(Baseline)* | 0.06 (6%) | **0.88 (88%)** | 0.12 | 0.7337 |
| **Random Forest** | 0.84 (84%) | 0.76 (76%) | 0.79 | 0.7717 |
| **XGBoost** | **0.89 (89%)** | 0.78 (78%) | **0.83** | **0.8360** |

### Principais Insights:
* A **Regressão Logística** entrega um Recall alto, mas sua precisão de apenas 6% significa que para cada fraude detectada, o banco bloquearia injustamente aproximadamente 15 clientes normais.
* O **XGBoost** destacou-se como o modelo ideal de produção, entregando **89% de Precisão** e **78% de Recall**, capturando a imensa maioria das fraudes com baixíssima taxa de alarmes falsos, o que preserva a experiência do cliente.

---

## 🧠 Explicabilidade das Decisões
O modelo de **Random Forest** nos permitiu extrair a relevância intrínseca dos atributos. As quatro variáveis que mais pesaram no processo de decisão para classificar uma transação como fraudulenta foram:
1. **V10**
2. **V14**
3. **V4**
4. **V12**

Essas componentes (derivadas do PCA original) são os principais indicadores de comportamento anômalo e padrões que distinguem compras normais de invasões de conta.

---

## ⚙️ Tecnologias Utilizadas
* **Python 3**
* **Pandas & NumPy** (Manipulação de Dados)
* **Scikit-Learn** (Pré-processamento, Divisão, Métricas e Modelagem)
* **XGBoost** (Classificador Avançado)
* **Matplotlib & Seaborn** (Visualizações Gráficas)

---

*Nota: Os dados utilizados neste projeto são públicos e foram obtidos diretamente do repositório oficial do Google Storage via API no momento da execução do notebook, protegendo a privacidade e conformidade.*
