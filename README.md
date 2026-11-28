# OcupaSmart HVAC

Pipeline de Ciência de Dados que prevê se uma sala está **ocupada ou vazia** a partir de sensores ambientais IoT, para automatizar o desligamento de ar-condicionado (HVAC) e iluminação e reduzir o custo de energia.

> Projeto Avaliativo — SCTEC Aperfeiçoar: Integração de IA com Dados (Módulo Único)

**Vídeo de apresentação:** [Link do video](https://drive.google.com/file/d/1yUbGAw92MX24mIytoIhHRI37Mra34inU/view?usp=sharing) 

---

## Problema

Salas técnicas e escritórios de um complexo industrial mantêm HVAC e luzes ligados mesmo vazios. Com sensores de temperatura, umidade, luminosidade e CO2, é possível prever a ocupação em tempo real e desligar os sistemas quando ninguém está presente.

A variável alvo é binária: `Occupancy = 1` (ocupado) ou `Occupancy = 0` (desocupado).

## Base de dados

[Occupancy Detection Dataset](https://archive.ics.uci.edu/dataset/357/occupancy+detection) (UCI Machine Learning Repository), arquivo `datatest.txt`.

| Variável | Descrição |
|---|---|
| `Temperature` | Temperatura (°C) |
| `Humidity` | Umidade relativa (%) |
| `Light` | Luminosidade (Lux) |
| `CO2` | Concentração de CO2 (ppm) |
| `HumidityRatio` | Umidade específica derivada de temperatura e umidade |
| `Occupancy` | Alvo: 0 = desocupado, 1 = ocupado |

## Técnicas e tecnologias

- **Linguagem:** Python 3.12 ou superior
- **Bibliotecas:** pandas, NumPy, Matplotlib, Seaborn, scikit-learn
- **Ambiente:** Jupyter Notebook

O notebook `projeto_ocupacao.ipynb` segue 7 fases:

1. **EDA:** dimensões, tipos, `describe()`, distribuição das classes, histogramas, mapa de calor de correlação e análise escrita.
2. **Data Prep:** remoção de duplicadas, imputação de nulos pela mediana (com justificativa) e boxplots para outliers.
3. **Feature Engineering:** nova variável `razao_co2_luz = CO2 / (Light + 1)`.
4. **Divisão dos dados:** 80% treino / 20% teste com `stratify=y` e `random_state=42`, evitando Data Leakage.
5. **Escalonamento:** `StandardScaler` apenas para o KNN (`fit_transform` no treino, `transform` no teste); Árvore sem escalonamento.
6. **Hiperparâmetros:** variação de `n_neighbors` (KNN) e `max_depth` (Árvore), comparando treino x teste para identificar overfitting.
7. **Avaliação final:** acurácia no teste, relatório de classificação, matriz de confusão e veredito.

## Como executar

```bash
# 1. Clonar o repositório
git clone https://github.com/igormr/OcupaSmart-HVAC.git
cd OcupaSmart-HVAC

# 2. Criar e ativar um ambiente virtual (opcional, recomendado)
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate

# 3. Instalar as dependências
pip install -r requirements.txt

# 4. Abrir o notebook e executar todas as células (Run All)
jupyter notebook projeto_ocupacao.ipynb
```

Na primeira execução o notebook baixa a base da UCI e salva em `data/occupancy.csv`. Todos os caminhos são relativos ao repositório.

## Estrutura do repositório

```
.
├── data/
│   └── occupancy.csv
├── projeto_ocupacao.ipynb
├── requirements.txt
├── .gitignore
└── README.md
```

## Resultados

Avaliação no conjunto de teste (533 amostras, 20% da base):

| Modelo | Melhor configuração | Acurácia no teste | Escalonamento |
|---|---|---|---|
| KNN | K = 1 | 98,31% | Sim |
| Árvore de Decisão | max_depth = 10 | 98,87% | Não |

A Árvore de Decisão foi um pouco melhor (cerca de 3 acertos a mais em 533) e ainda dispensa escalonamento, responde mais rápido e tem regras fáceis de auditar. Por isso é a recomendada para o sistema de automação predial.

O detalhe de overfitting, os relatórios de classificação e as matrizes de confusão estão nas Fases 6 e 7 do notebook.

## Melhorias futuras

- Usar `Pipeline` do scikit-learn com `SimpleImputer` e `StandardScaler` para evitar qualquer vazamento de dados.
- Escolher hiperparâmetros com validação cruzada (`GridSearchCV`) e usar o conjunto de teste uma única vez.
- Validar a generalização no tempo com `datatraining.txt` e `datatest2.txt` (a divisão aleatória de uma série temporal pode inflar a acurácia).
- Criar variáveis de horário (hora do dia, dia útil).
- Testar outros modelos (Random Forest, Gradient Boosting) e publicar como API para integração com o sistema predial.

## Autor

**Igor Moreira** — [GitHub](https://github.com/igormr) · [LinkedIn](https://www.linkedin.com/in/igormrr)
