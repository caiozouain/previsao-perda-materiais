# Previsão de Perdas de Materiais em Obras

Projeto de Machine Learning aplicado à previsão do índice de perda de materiais em obras.

A proposta é estimar o `indice_perda_real` de um material antes da execução do serviço, usando informações de planejamento, tipo de obra, serviço, material e contexto operacional. A partir da previsão, o projeto também simula uma recomendação de compra e uma estimativa de custo associado à perda esperada.

## Contexto

Em obras, a quantidade comprada de um material normalmente considera um índice de perda orçado ou uma referência histórica. Porém, esse percentual pode variar conforme o tipo de serviço, o material utilizado, a necessidade de corte, a complexidade da execução, a pressão de prazo e outras condições operacionais.

Este projeto utiliza uma base autoral, com campos inspirados em rotinas de planejamento, suprimentos e controle de materiais em obras.

## Objetivo

Prever o índice real de perda de materiais em obras e comparar o desempenho do modelo com uma referência simples baseada no índice de perda orçado.

Além da previsão, o projeto mostra como transformar o resultado do modelo em indicadores práticos:

* quantidade recomendada de compra;
* quantidade de perda prevista;
* custo estimado da perda;
* diferença em relação ao orçamento original.

## Estrutura do projeto

```text
previsao-perda-materiais/
├── data/
│   └── raw/
│       └── perda_material.csv
├── models/
│   └── modelo_perda_material.pkl
├── notebooks/
│   ├── 01_eda.ipynb
│   ├── 02_modelagem.ipynb
│   └── 03_simulacao.ipynb
├── README.md
├── requirements.txt
└── .gitignore
```

## Notebooks

### `01_eda.ipynb`

Realiza a análise exploratória da base de perdas de materiais.

O notebook avalia a distribuição do alvo `indice_perda_real`, compara perdas por serviço e material, analisa variáveis operacionais e separa as colunas que podem ou não ser usadas na modelagem.

Também são discutidos riscos de vazamento de dados, especialmente em colunas conhecidas apenas após a execução do serviço.

### `02_modelagem.ipynb`

Aplica a seleção de variáveis definida na EDA, realiza o pré-processamento e treina modelos de regressão.

Foram comparados:

* índice de perda orçado como baseline;
* regressão linear;
* Random Forest.

O pré-processamento foi feito com `ColumnTransformer` e `OneHotEncoder`, dentro de um `Pipeline`, para garantir consistência entre treino, teste e simulações futuras.

### `03_simulacao.ipynb`

Carrega o modelo salvo e aplica a previsão a um novo registro de material.

A saída mostra o índice de perda previsto, a quantidade recomendada de compra, a quantidade de perda esperada, o custo estimado da perda e a diferença em relação ao cálculo baseado no índice orçado.

## Variável-alvo

A variável prevista pelo modelo é:

```text
indice_perda_real
```

Ela representa o percentual real de perda observado após a execução do serviço.

## Principais variáveis utilizadas

Entre as variáveis de entrada consideradas no modelo estão:

* `tipo_obra`
* `fase_obra`
* `ambiente`
* `servico`
* `complexidade_execucao`
* `necessidade_corte_ajuste`
* `material`
* `tipo_item_material`
* `material_fragil`
* `material_modular`
* `custo_unitario`
* `quantidade_teorica`
* `indice_perda_orcado`
* `pressao_prazo`
* `qtd_frentes_simultaneas`

Colunas de identificação, colunas pós-execução e variáveis derivadas diretamente da perda real foram removidas da modelagem para evitar vazamento de dados.

## Resultados

O baseline usando diretamente o `indice_perda_orcado` apresentou erro maior que os modelos treinados.

| Modelo           |    MAE |   RMSE |      R² |
| ---------------- | -----: | -----: | ------: |
| Índice orçado    | 0,0510 | 0,0591 | -2,0878 |
| Regressão Linear | 0,0175 | 0,0232 |  0,5256 |
| Random Forest    | 0,0199 | 0,0258 |  0,4166 |

A regressão linear apresentou o melhor desempenho entre os modelos testados, reduzindo o MAE de aproximadamente 5,10 pontos percentuais no baseline para cerca de 1,75 ponto percentual.

A análise de importância das variáveis destacou principalmente:

* `material`
* `necessidade_corte_ajuste`
* `pressao_prazo`
* `complexidade_execucao`
* `servico`

Esse resultado é coerente com a lógica do problema, já que a perda de material depende tanto do item utilizado quanto das condições de execução.

## Tecnologias utilizadas

* Python
* pandas
* numpy
* matplotlib
* scikit-learn
* joblib
* Jupyter Notebook

## Como executar

Clone o repositório e instale as dependências:

```bash
pip install -r requirements.txt
```

Depois, execute os notebooks na ordem:

```text
01_eda.ipynb
02_modelagem.ipynb
03_simulacao.ipynb
```

## Observação sobre os dados

A base utilizada é autoral e foi estruturada para fins de estudo e portfólio, com campos inspirados em rotinas de planejamento, suprimentos e controle de materiais em obras.

## Possíveis melhorias futuras

* testar modelos adicionais, como Gradient Boosting;
* aplicar validação cruzada;
* realizar ajuste de hiperparâmetros;
* criar uma interface simples para simulação de novos registros;
* expandir a base com mais cenários de obra e materiais.
