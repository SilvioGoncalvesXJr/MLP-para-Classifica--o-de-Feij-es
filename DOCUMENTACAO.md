# Relatório Técnico: MLP para Classificação de Feijões (Dry Bean Dataset)
## Disciplina: Redes Neurais Artificiais - 2026.1

---

## 1. Visão Geral
Este documento apresenta os resultados sistemáticos do desenvolvimento de uma rede neural MLP para a classificação do *Dry Bean Dataset*. O foco principal foi a análise de hiperparâmetros, topologia e robustez estatística.

## 2. Metodologia
- **Dados:** 13.611 instâncias, 16 atributos morfológicos, 7 classes.
- **Pré-processamento:** Normalização Min-Max aplicada após a divisão dos dados para evitar *Data Leakage*.
- **Arquitetura:** MLP com camadas ocultas (ReLU) e camada de saída (Softmax).
- **Otimização:** Adam (estabilidade) e SGD com momentum (estudo de convergência).

---

## 3. Apresentação Sistemática de Resultados (Etapas 1 a 7)

### Etapa 1: Exploração Inicial (Baseline)
O objetivo foi verificar a estabilidade do modelo frente à inicialização aleatória de pesos. Foram executadas 5 seeds diferentes.

**Evidência Visual:** `q1_convergencia.png` (Curvas de Loss e Acurácia sobrepostas).

| Seed | Loss Final | Acurácia (%) | Tempo (s) |
|------|------------|--------------|-----------|
| 0    | 0.1938     | 92.75        | 100.2     |
| 7    | 0.1944     | 92.69        | 145.1     |
| 21   | 0.1967     | 92.69        | 135.5     |
| 42   | 0.1957     | 92.88        | 140.3     |
| 99   | 0.1930     | 93.02        | 136.3     |
| **Média** | **0.1947** | **92.81** | **131.5s** |

---

### Etapa 2: Busca de Hiperparâmetros (Grid Search)
Análise do impacto da Taxa de Aprendizado (LR) e Momento usando o otimizador SGD.

**Evidência Visual:** `q2_hiperparametros.png` (Gráficos comparativos de convergência).

| LR | Momento | Épocas | Loss Final | Convergiu (<=0.001) | Tempo (s) |
|----|---------|--------|------------|---------------------|-----------|
| 0.01 | 0.9     | 59     | 0.2050     | ❌                  | 72.6      |
| 0.1  | 0.7     | 24     | 0.2310     | ❌                  | 41.0      |
| 0.1  | 0.9     | 20     | 0.2627     | ❌                  | 25.9      |
| 0.001| 0.9     | 200    | 0.2153     | ❌                  | 324.4     |

*Nota: O SGD demonstrou dificuldade em atingir a meta de 0.001 no tempo estipulado, atingindo um platô em torno de 0.20.*

---

### Etapa 3: Exploração da Topologia
Variação do número de camadas ocultas (1-3) e neurônios (10-100).

**Evidência Visual:** `q3_heatmap_f1.png` e `q3_underfitting_overfitting.png`.

| Camadas | Neurônios | Loss Treino | Loss Val | F1 (Weighted) | Diagnóstico |
|---------|-----------|-------------|----------|---------------|-------------|
| 2       | 50        | 0.1928      | 0.2208   | 0.9242        | Adequado    |
| 1       | 100       | 0.2075      | 0.2198   | 0.9235        | Adequado    |
| 3       | 100       | 0.1712      | 0.2614   | 0.9132        | Overfitting |

---

### Etapa 4: Sensibilidade a Dados de Treinamento
Análise do impacto do volume de dados (20% a 100%) na performance.

**Evidência Visual:** `q4_quantidade_dados.png`.

| Proporção (%) | Nº Exemplos | Loss Val | F1-Score | Tempo (s) |
|---------------|-------------|----------|----------|-----------|
| 20%           | 1524        | 0.2409   | 0.9103   | 28.1      |
| 60%           | 4572        | 0.2175   | 0.9161   | 44.2      |
| 100%          | 7621        | 0.2395   | 0.9192   | 53.9      |

---

### Etapa 5: Influência dos Atributos (Feature Selection)
Uso de Informação Mútua para reduzir a dimensionalidade.

**Evidência Visual:** `q5_feature_importance.png`.

| Subconjunto | Nº Atributos | F1 Média | F1 Std | Acurácia Média(%) | Tempo Médio (s) |
|-------------|--------------|----------|--------|-------------------|-----------------|
| Todos (16)  | 16           | 0.9233   | 0.0057 | 92.37             | 59.1            |
| Top-8       | 8            | 0.9057   | 0.0023 | 90.65             | 59.1            |
| Top-4       | 4            | 0.8228   | 0.0019 | 82.57             | 63.8            |

---

### Etapa 6: Validação Inicial (Conjunto de Teste)
As 4 melhores topologias da Etapa 3 avaliadas no conjunto de TESTE (inédito).

| Configuração | Loss Treino | Loss Teste | Acurácia Teste (%) | F1-Score Teste |
|--------------|-------------|------------|--------------------|----------------|
| 2L×50N       | 0.1932      | 0.2063     | 92.16              | 0.9216         |
| 2L×30N       | 0.2023      | 0.2090     | 92.12              | 0.9211         |

---

### Etapa 7: Validação Cruzada K-Fold (Robustez Final)
Aplicação de 10-Fold Cross-Validation na configuração campeã (2 camadas x 50 neurônios).

**Evidência Visual:** `q7_validacao_cruzada.png` e Matriz de Confusão Final.

**Resumo Estatístico (10 Folds):**

| Métrica | Média | Desvio-Padrão (σ) | Variância (σ²) |
|---------|-------|-------------------|----------------|
| **Loss** | 0.2003 | 0.0195 | 0.00038 |
| **Acurácia (%)** | 92.44% | 1.11% | 1.2321 |
| **F1-Score** | 0.9242 | 0.0112 | 0.00012 |

---

## 4. Conclusão
O modelo final demonstrou **alta robustez estatística**, com variância mínima entre os folds da validação cruzada. A metodologia de pré-processamento sem vazamento de dados e o uso de critérios de parada eficientes garantiram um modelo com excelente capacidade de generalização para o problema de classificação de feijões.
