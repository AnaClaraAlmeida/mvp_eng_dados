# MVP Yelp 
## Visão Geral
Este projeto tem como objetivo a construção de um pipeline de dados em nuvem,
utilizando a plataforma Databricks Community Edition, contemplando as etapas de
busca, coleta, modelagem, carga e análise de dados.

O projeto utiliza dados públicos do Yelp Open Dataset e foi desenvolvido como
parte do MVP da disciplina de Engenharia de Dados.

---

## Objetivo
O objetivo deste MVP é construir um pipeline de dados capaz de suportar análises
sobre avaliações de estabelecimentos, respondendo às seguintes perguntas:

1. Qual é a distribuição das notas médias dos estabelecimentos?
2. Estabelecimentos com maior volume de avaliações tendem a apresentar notas médias diferentes?
3. Quais categorias de estabelecimentos apresentam as maiores notas médias?
4. Como se distribuem as notas individuais dadas pelos usuários?

Todas as peguntas foram [respondidas aqui](https://github.com/AnaClaraAlmeida/mvp_eng_dados/blob/main/notebooks/com_output/04_analise_dados.ipynb).

---

## Plataforma e Tecnologias
- Databricks Community Edition
- Apache Spark (PySpark)
- Delta Lake
- SQL
- Python (matplotlib, pandas)

---

## Conjunto de Dados
- Fonte: Yelp Open Dataset (Kaggle)  
  https://www.kaggle.com/datasets/yelp-dataset/yelp-dataset

### Arquivos utilizados
- `yelp_academic_dataset_business.json` (arquivo completo)
- `yelp_academic_dataset_review.json` (amostra)

Devido à limitação de upload de arquivos da versão Community do Databricks (5 GB),
foi utilizada uma amostra de 3.000.000 de registros do arquivo de avaliações,
preservando o formato original dos dados.

**Catálogo de Dados**: [link](https://github.com/AnaClaraAlmeida/mvp_eng_dados/blob/main/docs/data_catalog.md)

---

## Arquitetura do Pipeline

[Kaggle Dataset](https://www.kaggle.com/datasets/yelp-dataset/yelp-dataset/data?select=yelp_academic_dataset_business.json) -> Upload em Volume (Unity Catalog) -> [Bronze (dados brutos)](https://github.com/AnaClaraAlmeida/mvp_eng_dados/blob/main/notebooks/com_output/01_ingestao_bronze.ipynb) -> [Silver (flat por conceito)](https://github.com/AnaClaraAlmeida/mvp_eng_dados/blob/main/notebooks/com_output/02_transformacao_silver.ipynb) -> [Gold (modelo estrela)](https://github.com/AnaClaraAlmeida/mvp_eng_dados/blob/main/notebooks/com_output/03_modelagem_gold.ipynb) -> [Análise de dados](https://github.com/AnaClaraAlmeida/mvp_eng_dados/blob/main/notebooks/com_output/04_analise_dados.ipynb)


---

## Modelagem de Dados
- **Camada Silver**: tabelas flat por conceito
  - `silver_yelp_business`
  - `silver_yelp_review`

- **Camada Gold**: modelo estrela
  - Dimensão: `gold_dim_business`
  - Fato: `gold_fact_review`

A modelagem foi documentada detalhadamente no Catálogo de Dados.

---

## Qualidade dos Dados

Foi realizada uma análise de qualidade dos dados nas camadas Silver e Gold,
avaliando domínios numéricos, completude e integridade referencial.

Os dados apresentaram boa qualidade, sem valores nulos em campos críticos e
com 100% de correspondência entre avaliações e estabelecimentos.

Detalhes da análise encontram-se no Catálogo de Dados.

- Catálogo de Dados: [link](https://github.com/AnaClaraAlmeida/mvp_eng_dados/blob/main/docs/data_catalog.md) 

---

## Análise dos Dados
As análises foram realizadas utilizando SQL sobre a camada Gold, com apoio de
visualizações em Python para facilitar a interpretação dos resultados.

Os principais padrões observados incluem:
- Predominância de avaliações positivas
- Maior estabilidade de notas médias em estabelecimentos com maior volume de reviews
- Destaque de categorias ligadas a serviços especializados entre as mais bem avaliadas

As análises completas estão disponíveis no notebook `04_analise_dados` ([ACESSE AQUI](https://github.com/AnaClaraAlmeida/mvp_eng_dados/blob/main/notebooks/com_output/04_analise_dados.ipynb)).

E aqui estão alguns exemplo de gráficos obtidos:

Pergunta 1:

![Distribuição das notas médias dos estabelecimentos](https://raw.githubusercontent.com/AnaClaraAlmeida/mvp_eng_dados/main/prints/analise_notas_estabelecimentos.png)

Pergunta 3:

![Categorias com maiores notas médias](https://raw.githubusercontent.com/AnaClaraAlmeida/mvp_eng_dados/main/prints/analise_categorias.png)

---

## Limitações e Trabalhos Futuros
- Uso de amostra do conjunto de avaliações devido a limitações da plataforma
- Campo de categorias não normalizado
- Possibilidade de expansão do modelo com dimensões adicionais (ex.: categorias, tempo)

---
## Autoavaliação

Antes do início da pós-graduação, eu já atuava com análise de dados, utilizando
principalmente SQL, Python e, de forma mais pontual, a plataforma Databricks.
Apesar dessa experiência, meu contato com conceitos mais estruturados de
engenharia de dados ainda era mais limitado.

O desenvolvimento do MVP me permitiu aplicar, de forma prática, os conceitos
abordados na disciplina, especialmente no que diz respeito à construção de
pipelines de dados em nuvem, à organização dos dados em camadas (Bronze, Silver e
Gold) e à modelagem analítica em esquema estrela. O conhecimento prévio em análise
de dados e SQL facilitou a exploração do conjunto de dados, a definição das
perguntas e a interpretação dos resultados obtidos.

Os principais desafios foram a limitação de upload da versão
Community do Databricks, que exigiu a utilização de uma amostra do conjunto de
avaliações. Além disso, houve um período inicial de adaptação ao ambiente do
Databricks e ao uso do PySpark para transformação e persistência dos dados. Essas
dificuldades foram superadas por meio da consulta à documentação oficial,
exploração prática da plataforma e testes a construção do
pipeline.

Como pontos fortes ao longo do trabalho, destaco a capacidade de estruturar
consultas analíticas em SQL, a organização lógica do pipeline e a facilidade de
adaptação a novas ferramentas, decorrente da minha experiência prévia com análise
de dados e programação. O pipeline desenvolvido contemplou todas as etapas
propostas que foram a busca, coleta, modelagem, carga e análise dos dados.

Como trabalhos futuros, este projeto pode ser expandido com a normalização das
categorias dos estabelecimentos, a inclusão de novas dimensões analíticas e a
automatização da ingestão dos dados. Além disso, a execução do pipeline em
ambientes com maior capacidade computacional permitiria a análise do conjunto
completo de avaliações, aproximando o projeto de um cenário mais próximo ao de
produção.